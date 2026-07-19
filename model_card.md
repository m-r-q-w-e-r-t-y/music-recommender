# 🎧 Model Card: Music Recommender Simulation

## 1. Model Name  

**VibeMatch 1.0**

---

## 2. Intended Use  

VibeMatch takes a short taste profile (favorite genre, favorite mood, target energy, and whether the user likes acoustic songs) and returns the top 5 songs from a fixed 25-song catalog that best match it, along with a plain-language reason for each pick. It assumes the user can state their preferences directly rather than inferring them from listening history. This is a classroom simulation meant to demonstrate how content-based recommendation works, not a production system — it shouldn't be used to make real recommendations for real listeners or treated as representative of how a commercial platform like Spotify actually ranks songs.

---

## 3. How the Model Works  

Every song gets checked against your taste profile and earns points for how well it matches. An exact genre match is worth the most points, a mood match is worth half that, and how close the song's energy is to what you asked for earns you up to a similar amount — the closer the energy, the more points, even if it's not a perfect match. If you said you like acoustic songs, an acoustic-sounding song gets a small bonus (and the reverse is true if you said you don't). Every song in the catalog gets scored this way, then they're sorted from highest to lowest score and the top 5 are shown to you, each with a short list of reasons explaining why it scored the way it did. The starter file had no logic at all — everything from the CSV loading to the point values to the sorting was written from scratch based on the recipe planned in Phase 2.

---

## 4. Data  

The catalog has 25 songs, expanded from the original 10-song starter file. It spans 17 genres (pop, lofi, rock, ambient, jazz, synthwave, indie pop, edm, country, soul, hip-hop, punk, classical, reggae, k-pop, funk, blues) and 15 moods (happy, chill, intense, relaxed, moody, focused, euphoric, nostalgic, passionate, confident, angry, peaceful, energetic, upbeat, melancholy), plus numeric fields for energy, tempo, valence, danceability, and acousticness. Most genres only have 1-2 songs, though, so the catalog is wide but shallow — it's better at representing broad genre variety than at giving deep options within any one genre. Lyrics, artist popularity, release year, and anything about how a song actually sounds beyond these five numbers aren't captured at all.

---

## 5. Strengths  

It works best for users whose genre has decent depth in the catalog, like lofi (4 songs) — the Chill Lofi profile's top 4 results were genuinely lofi, chill, and acoustic, which matches intuition well. The energy-closeness scoring also behaves correctly in isolation: songs get more credit the nearer their energy is to the target, rather than just rewarding "high energy" outright, so a low-energy profile properly favors calm songs instead of loud ones. The reasons attached to each recommendation are accurate reflections of the math, not generic text, which makes it easy to trust or double-check any given result.

---

## 6. Limitations and Bias 

Genre carries the most weight (+2.0), so it tends to dominate the ranking even when a song's mood or energy is a much better fit — a same-genre song with the wrong mood can outrank a different-genre song that matches on everything else. The catalog is also uneven: some genres (lofi, pop, rock) have 3-4 songs while others (classical, funk, blues) have only one, so users with niche tastes get thin, less confident recommendations, while pop/lofi fans get more refined ones. High-energy songs like "Gym Hero" (0.93 energy) keep resurfacing across very different profiles just because they sit near the top of the energy scale, which is a form of popularity/energy bias unrelated to actual genre or mood fit. The scoring also treats genre and mood as single exact-match strings, so it can't recognize that "indie pop" is close to "pop," or that "chill" and "relaxed" are similar moods — near-misses get zero credit instead of partial credit.

---

## 7. Evaluation  

Tested three profiles: High-Energy Pop (`genre=pop, mood=happy, energy=0.8`), Chill Lofi (`genre=lofi, mood=chill, energy=0.3, likes_acoustic=True`), and Deep Intense Rock (`genre=rock, mood=intense, energy=0.9`). Full outputs are in the README's "Sample Recommendation Output" section.

Comparing them: the Pop and Rock profiles both surface "Gym Hero" in their top 5 despite it belonging to neither genre and matching neither mood — it's simply one of the highest-energy songs in the catalog (0.93), so it rides the energy score alone into a strong position. The Chill Lofi profile behaves the most cleanly: because it uses `likes_acoustic` in addition to genre/mood/energy, all four songs at the top are genuinely lofi and chill, and the acousticness bonus reinforces rather than distorts that. The Rock profile is the least reliable of the three because the catalog only has two rock songs — after those, the list is padded out with unrelated high-energy tracks (EDM, k-pop) that happen to share nothing but energy. Nothing here felt truly surprising once we knew the recipe, but it confirmed that genre depth in the dataset matters as much as the scoring weights: a profile can only be well-served if enough matching songs exist for it in the first place.

---

## 8. Future Work  

1. Add a diversity penalty so the top 5 doesn't lean on one artist or genre when the catalog is thin in a given area (Optional Challenge 3 in the instructions covers this).
2. Let genre and mood match partially instead of only exactly — e.g., give "indie pop" partial credit against a "pop" preference, and treat "chill" and "relaxed" as close moods, instead of scoring near-misses as zero.
3. Expand the catalog so every genre has at least 3-4 songs, which would fix the biggest issue found during testing (thin genres like rock getting padded out with unrelated high-energy tracks).

---

## 9. Personal Reflection  

The biggest learning moment was seeing how much a single weight can change the *feel* of a recommender — dropping genre from +2.0 to +1.0 in the weight-shift experiment let raw energy override genre matches in a handful of cases, and the results immediately felt less intentional even though nothing about the code was "wrong." AI helped speed up writing the CSV loading and sorting logic, but I still had to double-check the actual point math by hand against the printed "reasons" output, since it's easy for a scoring formula to look right and still return numbers that don't match the intended recipe. What surprised me most is how convincing a handful of if-statements and a sort can feel — "Gym Hero" kept showing up across very different profiles for a reason that had nothing to do with taste (it's just a very high-energy song), which is a small-scale preview of the kind of popularity bias real platforms have to actively guard against. If I extended this, I'd want the catalog to be bigger and more evenly spread across genres before trusting the rankings much further.

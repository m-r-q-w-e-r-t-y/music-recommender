# 🎵 Music Recommender Simulation

## Project Summary

In this project you will build and explain a small music recommender system.

Your goal is to:

- Represent songs and a user "taste profile" as data
- Design a scoring rule that turns that data into recommendations
- Evaluate what your system gets right and wrong
- Reflect on how this mirrors real world AI recommenders

Replace this paragraph with your own summary of what your version does.

---

## How The System Works

Real recommenders like Spotify mostly work two ways: collaborative filtering (what similar users liked) and content-based filtering (matching a song's own traits to your taste). This project only does the second one — no other users, just song attributes vs. one taste profile.

**Features used:** genre, mood, energy, acousticness (from `songs.csv`). Tempo, valence, and danceability are in the data but not scored yet.

**`Song` fields:** id, title, artist, genre, mood, energy, tempo_bpm, valence, danceability, acousticness
**`UserProfile` fields:** favorite_genre, favorite_mood, target_energy, likes_acoustic

**Scoring recipe:**
- genre match: +2.0
- mood match: +1.0
- energy closeness: up to +2.0 (closer to target = more points)
- acousticness fits `likes_acoustic`: +0.5

Genre is weighted highest, so it'll probably dominate — a song with your genre but wrong mood may still outrank a perfect mood/energy match in another genre.

**Example user profile:**
```
{"favorite_genre": "rock", "favorite_mood": "intense", "target_energy": 0.9, "likes_acoustic": False}
```
This is specific enough to separate "intense rock" from "chill lofi" (genre, mood, and energy all point opposite ways) but two rock subgenres with the same mood/energy would score identically — the profile can't tell them apart.

Scoring and ranking are two separate jobs: `score_song` judges one song in isolation, `recommend_songs` runs that judge over every song and sorts the results — you need both because a score alone isn't a recommendation until it's compared against everything else.

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows

2. Install dependencies

```bash
pip install -r requirements.txt
```

3. Run the app:

```bash
python -m src.main
```

### Running Tests

Run the starter tests with:

```bash
pytest
```

You can add more tests in `tests/test_recommender.py`.

---

## Sample Recommendation Output

Three profiles are run in `main.py`: High-Energy Pop, Chill Lofi, and Deep Intense Rock.

```
Loaded songs: 25

=== High-Energy Pop ({'genre': 'pop', 'mood': 'happy', 'energy': 0.8}) ===
Top recommendations:

Sunny Side Up - Score: 5.00
Because: genre match (+2.0), mood match (+1.0), energy closeness (+2.00)

Sunrise City - Score: 4.96
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.96)

Gym Hero - Score: 3.74
Because: genre match (+2.0), energy closeness (+1.74)

Golden Hour - Score: 2.96
Because: mood match (+1.0), energy closeness (+1.96)

Rooftop Lights - Score: 2.92
Because: mood match (+1.0), energy closeness (+1.92)


=== Chill Lofi ({'genre': 'lofi', 'mood': 'chill', 'energy': 0.3, 'likes_acoustic': True}) ===
Top recommendations:

Window Seat - Score: 5.44
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.94), acousticness fit (+0.5)

Library Rain - Score: 5.40
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.90), acousticness fit (+0.5)

Tea and Vinyl - Score: 5.34
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.84), acousticness fit (+0.5)

Midnight Coding - Score: 5.26
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.76), acousticness fit (+0.5)

Focus Flow - Score: 4.30
Because: genre match (+2.0), energy closeness (+1.80), acousticness fit (+0.5)


=== Deep Intense Rock ({'genre': 'rock', 'mood': 'intense', 'energy': 0.9}) ===
Top recommendations:

Overdrive - Score: 5.00
Because: genre match (+2.0), mood match (+1.0), energy closeness (+2.00)

Storm Runner - Score: 4.98
Because: genre match (+2.0), mood match (+1.0), energy closeness (+1.98)

Gym Hero - Score: 2.94
Because: mood match (+1.0), energy closeness (+1.94)

Neon Pulse - Score: 1.90
Because: energy closeness (+1.90)

Neon Skyline - Score: 1.90
Because: energy closeness (+1.90)
```

**Accuracy check:** the Deep Intense Rock profile "feels" right — the top two are actual rock songs by the same artist (Voltline), and nothing weird sneaks in until 4th/5th place, where high-energy songs from unrelated genres (EDM, k-pop) show up purely on energy closeness since rock only has two songs in the catalog. Gym Hero (pop) keeps appearing near the top of every energetic profile because it's one of the highest-energy songs overall (0.93) — that's a sign genre + energy are doing most of the work and the catalog may not have enough per-genre depth to fully separate tastes.

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

**Weight shift experiment:** temporarily changed genre match from +2.0 to +1.0 and energy closeness from up to +2.0 to up to +4.0, then reran all three profiles.

- High-Energy Pop: Golden Hour and Rooftop Lights (indie pop, mood match, no genre match) jumped ahead of Gym Hero (pop genre match, no mood match) — energy + mood together now outweigh a lone genre match.
- Chill Lofi: top 4 stayed the same lofi songs, but Spacewalk Thoughts (ambient, not lofi) cracked the top 5 purely on energy + acousticness.
- Deep Intense Rock: Neon Pulse and Neon Skyline (EDM/k-pop, no genre or mood match at all) entered the top 5 just for having high raw energy.

This didn't make the recommendations more accurate — it made them noisier. Genre weighting at 2.0 was doing useful work keeping off-genre songs out of the list; cutting it in half let raw energy override genre in a few cases, which felt less intentional. The change was reverted back to the original weights (genre +2.0, energy up to +2.0) after the experiment.

---

## Limitations and Risks

Summarize some limitations of your recommender.

Examples:

- It only works on a tiny catalog
- It does not understand lyrics or language
- It might over favor one genre or mood

You will go deeper on this in your model card.

---

## Reflection

Read and complete `model_card.md`:

[**Model Card**](model_card.md)

Write 1 to 2 paragraphs here about what you learned:

- about how recommenders turn data into predictions
- about where bias or unfairness could show up in systems like this




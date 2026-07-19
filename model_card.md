# 🎧 Model Card: Music Recommender Simulation

## 1. Model Name  

Give your model a short, descriptive name.  
Example: **VibeFinder 1.0**  

---

## 2. Intended Use  

Describe what your recommender is designed to do and who it is for. 

Prompts:  

- What kind of recommendations does it generate  
- What assumptions does it make about the user  
- Is this for real users or classroom exploration  

---

## 3. How the Model Works  

Explain your scoring approach in simple language.  

Prompts:  

- What features of each song are used (genre, energy, mood, etc.)  
- What user preferences are considered  
- How does the model turn those into a score  
- What changes did you make from the starter logic  

Avoid code here. Pretend you are explaining the idea to a friend who does not program.

---

## 4. Data  

Describe the dataset the model uses.  

Prompts:  

- How many songs are in the catalog  
- What genres or moods are represented  
- Did you add or remove data  
- Are there parts of musical taste missing in the dataset  

---

## 5. Strengths  

Where does your system seem to work well  

Prompts:  

- User types for which it gives reasonable results  
- Any patterns you think your scoring captures correctly  
- Cases where the recommendations matched your intuition  

---

## 6. Limitations and Bias 

Genre carries the most weight (+2.0), so it tends to dominate the ranking even when a song's mood or energy is a much better fit — a same-genre song with the wrong mood can outrank a different-genre song that matches on everything else. The catalog is also uneven: some genres (lofi, pop, rock) have 3-4 songs while others (classical, funk, blues) have only one, so users with niche tastes get thin, less confident recommendations, while pop/lofi fans get more refined ones. High-energy songs like "Gym Hero" (0.93 energy) keep resurfacing across very different profiles just because they sit near the top of the energy scale, which is a form of popularity/energy bias unrelated to actual genre or mood fit. The scoring also treats genre and mood as single exact-match strings, so it can't recognize that "indie pop" is close to "pop," or that "chill" and "relaxed" are similar moods — near-misses get zero credit instead of partial credit.

---

## 7. Evaluation  

Tested three profiles: High-Energy Pop (`genre=pop, mood=happy, energy=0.8`), Chill Lofi (`genre=lofi, mood=chill, energy=0.3, likes_acoustic=True`), and Deep Intense Rock (`genre=rock, mood=intense, energy=0.9`). Full outputs are in the README's "Sample Recommendation Output" section.

Comparing them: the Pop and Rock profiles both surface "Gym Hero" in their top 5 despite it belonging to neither genre and matching neither mood — it's simply one of the highest-energy songs in the catalog (0.93), so it rides the energy score alone into a strong position. The Chill Lofi profile behaves the most cleanly: because it uses `likes_acoustic` in addition to genre/mood/energy, all four songs at the top are genuinely lofi and chill, and the acousticness bonus reinforces rather than distorts that. The Rock profile is the least reliable of the three because the catalog only has two rock songs — after those, the list is padded out with unrelated high-energy tracks (EDM, k-pop) that happen to share nothing but energy. Nothing here felt truly surprising once we knew the recipe, but it confirmed that genre depth in the dataset matters as much as the scoring weights: a profile can only be well-served if enough matching songs exist for it in the first place.

---

## 8. Future Work  

Ideas for how you would improve the model next.  

Prompts:  

- Additional features or preferences  
- Better ways to explain recommendations  
- Improving diversity among the top results  
- Handling more complex user tastes  

---

## 9. Personal Reflection  

A few sentences about your experience.  

Prompts:  

- What you learned about recommender systems  
- Something unexpected or interesting you discovered  
- How this changed the way you think about music recommendation apps  

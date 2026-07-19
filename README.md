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

Default profile: `genre=pop, mood=happy, energy=0.8`

```
Loaded songs: 25

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
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

Use this section to document the experiments you ran. For example:

- What happened when you changed the weight on genre from 2.0 to 0.5
- What happened when you added tempo or valence to the score
- How did your system behave for different types of users

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




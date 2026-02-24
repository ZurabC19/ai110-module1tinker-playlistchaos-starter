# Playlist Chaos – Week 1 Tinker Activity

## Activity Summary
The purpose of this activity was to practice AI-assisted debugging by exploring a small Streamlit music app and improving its logic and reliability. I examined how songs move from the UI into the backend playlist engine and how classification and statistics are computed. The most confusing area for students is likely the interaction between energy thresholds, keywords, and normalization, which can lead to songs appearing in unexpected playlists. AI assistance was helpful for explaining functions and tracing data flow, but sometimes suggested changes that ignored profile settings or overall app intent. To guide a student without giving answers, I would suggest printing song dictionaries or playlist stats in the UI (e.g., using `st.write`) so they can see exactly what data the logic is using.

---

## 1. Investigation: Observed Issues

While exploring the original app, I identified several inconsistencies:

- **Search Failure:** Searching for an artist substring (e.g., `"ac"`) did not return expected matches like `"ac/dc"`.
- **Incorrect Average Energy:** The average energy statistic only reflected the Hype playlist instead of all songs.
- **Skewed Hype Ratio:** The ratio often appeared as `1.00` because hype count was divided by itself.
- **Lucky Pick Exclusion:** The “any” mode ignored Mixed songs and could fail when playlists were empty.
- **Normalization Issues:** Genre and energy inputs were not consistently parsed, causing mismatches or invalid values.

---

## 2. Technical Fixes

I implemented the following fixes primarily in `playlist_logic.py`:

**Fix 1: Search Logic Correction**  
- **Source:** `search_songs()`  
- **Problem:** Code checked `value in q`, reversing the intended substring search.  
- **Correction:** Changed to `q in value` so queries match inside the song field.

**Fix 2: Global Average Energy Calculation**  
- **Source:** `compute_playlist_stats()`  
- **Problem:** Energy sum used only Hype songs.  
- **Correction:** Iterated through `all_songs` to compute a true library average.

**Fix 3: Hype Ratio Denominator**  
- **Source:** `compute_playlist_stats()`  
- **Problem:** Ratio used hype count as both numerator and denominator.  
- **Correction:** Set denominator to total songs across all playlists.

**Fix 4: Inclusive Lucky Pick**  
- **Source:** `lucky_pick()`  
- **Problem:** “Any” mode excluded Mixed songs and could crash on empty lists.  
- **Correction:** Added Mixed playlist to pool and safe-checked empty selection.

**Fix 5: Robust Normalization**  
- **Source:** `normalize_song()`  
- **Problem:** Genre and energy values were not consistently sanitized.  
- **Correction:** Added trimming, lowercase normalization, and safe numeric conversion.

**Fix 6: Energy-First Classification**  
- **Source:** `classify_song()`  
- **Problem:** Genre/keyword matches could override user energy thresholds.  
- **Correction:** Prioritized energy thresholds so songs only enter Hype if they meet the configured minimum.

---

## 3. How the code is organized

**app.py**  
The Streamlit UI handling the mood profile, song input, playlist display, Lucky Pick controls, and statistics.

**playlist_logic.py**  
The backend engine responsible for normalization, classification rules, playlist construction, searching, and statistics calculations.

---

## 4. Tips for Success

- **Use AI as a reasoning partner:** Asking what a specific function does was more useful than asking for a full fix.
- **Test after each change:** Refreshing the Streamlit app immediately revealed whether stats and playlists updated correctly.
- **Visual debugging helps:** Printing playlists or session state in the UI clarifies how data moves through the app.
- **Start small:** Fixing one concrete issue at a time made the codebase easier to understand.

---

## Summary

This activity reinforced how exploring an application and its codebase together helps locate and fix logic issues. Students may struggle most with identifying subtle bugs in statistics or classification rules and with prompting AI precisely enough to get useful explanations. AI was effective for understanding functions and tracing data flow, but could be misleading when prompts were vague or ignored context. Encouraging students to experiment with the UI, inspect data, and refine prompts helps them use AI more effectively for debugging and reasoning about program behavior.

# Polarity-Aware Claim Linking
### Author: Krti Tallam

Hi Yi — here’s the succinct write-up of the idea I floated today about tracking negation explicitly.

---

## 1  Why I’m Thinking About This

I love that we already extract **atomic claims** — tiny, self-contained assertions.  
The catch: when I embed those sentences with off-the-shelf models (BERT, MiniLM, etc.), they happily blur negation:

`“She won the election.” ≈ “She failed to win the election.”`


In vector space, they’re practically neighbors even though they contradict each other. That’s a problem if we want to surface disagreements or support relationships automatically.

---

## 2  My Lightweight Fix — Add a `polarity` Field

| Step | What I Do | Why It Helps |
|------|-----------|--------------|
| **Normalize proposition** | Strip negators / surface fluff → canonical form (e.g., `Won(She, Election)`). | Keeps embeddings stable. |
| **Tag polarity** | Add `"polarity": "+"` for affirm, `"polarity": "-"` for negation. | Captures truth intent explicitly. |
| **Embed only the canonical text** | Feed `Won(She, Election)` into the model. | I avoid retraining or logic-aware embeddings. |
| **Post-hoc reasoning** | Use `(proposition, polarity)` pairs to flag contradictions, consensus, etc. | Gives us a tiny logical layer with zero ML overhead. |

---

## 3  Symbolic Flavor

```text
<Polarity> ⊢ <Proposition>

### Examples
+ ⊢ Won(She, Election)
- ⊢ Available(Product)

And, a JSON Sketch:
```
{
  "claim_id": "clim001",
  "polarity": "-",
  "proposition": "Available(Product)",
  "raw_text": "The product is no longer available."
}
```
Contradiction check == same proposition + opposite polarity.

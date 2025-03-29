# IMoE (InterMindofExperts)
## Things to know:
- Multiple variables thrown around, consult glossary for description.
- Architecture by a single 14-year-old with a vision, code made by Claude 3.7 Sonnet via API, as a means of implementation.
- Open source, in my opinion, is better than proprietary.
## Section 1: What is IMoE?
IMoE is a unique LLM architecture designed to maximize power without sacrificing efficiency. It's also intended to solve prevalent pain points that plague pioneers processing these problems.
## Section 2: How does it work?
It solves many things through many ways, but the basis of everything is the 3 core parts:
### Classifier:
- Classifier (unsupervised) discovers num_C categories on its own from your training data, increasing accuracy and ease of use.
- Classifier then is run on training data, separating it into each category. If an example/sample belongs to multiple categories according to the classifier, it's considered to be in each category. If it's not in a category, it goes in every category.
- Classifier determines if it's in a category by seeing if it crosses a threshold, t_C.
### Experts:
- Staying true to MoE, this architecture has experts.
- Many things are different, however.
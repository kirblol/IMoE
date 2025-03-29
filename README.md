# IMoE (InterMindofExperts)
# Things to know:
- This architecture has many variables unique to IMoE, so there's a separate GLOSSARY.md file.
- Architecture by a single 14-year-old with a vision, code made by Claude 3.7 Sonnet via API, as a means of implementation.
- This repo is open source because it fuels the advancement of AI much faster.
# Section 1: What is IMoE?
IMoE is a unique LLM architecture designed to maximize power without sacrificing efficiency. It's also intended to solve prevalent pain points that plague pioneers processing these problems.
# Section 2: How does it work at a very high level?
It solves many things through many ways, but the basis of everything is the 3 core parts. They are trained separately, but all help each other in the final system:
### Classifier:
- Classifier determines what categor(y/ies) the input fits to so it knows what expert to route to.
### Experts:
- Staying true to MoE, this architecture has experts, but they're trained on their specific category.
### Summarizer:
- Not a traditional summarizer. It's something that can take abstract concepts and ground them.
- For example, if an IMoE model is asked to code, and one expert simply says to code, and the other gives some constraints, the summarizer must code within those constraints.
# Section 3: Deep dive for the nerds (like me):
Now that the surface jump is done, let's dive head first into exactly how everything works:
## Training Steps:
### Classifier:
1. First, you input the dataset, either from huggingface or a txt corpus.
2. Next, the classifier trains unsupervised on this data, discovering 
# IMoE (InterMindofExperts)
## Things to know:
- This architecture has many variables unique to IMoE, so there's a glossary.
- Architecture by a single 14-year-old with a vision, code made by Claude 3.7 Sonnet via API, as a means of implementation.
- This repo is open source because it fuels the advancement of AI much faster.
## Section 1: What is IMoE?
IMoE is a unique LLM architecture designed to maximize power without sacrificing efficiency. It's also intended to solve prevalent pain points that plague pioneers processing these problems.
## Section 2: How does it work at a very high level?
It solves many different things in many different ways, but the basis of everything is the 3 core parts. They are trained separately, but all help each other in the final system:
### Classifier:
- The classifier determines what category or categories the input fits so it knows what expert to route to.
### Experts:
- Staying true to MoE, this architecture has experts, but they're trained in their specific category.
### Summarizer:
- Not a traditional summarizer. It's something that can take abstract concepts and ground them.
- For example, if an IMoE model is asked to code, and one expert simply says to code, and the other gives some constraints, the summarizer must code within those constraints.
## Section 3: Deep dive for the nerds (like me):
Now that the surface jump is done, let's dive headfirst into exactly how everything works:
### Training Steps:
#### Classifier (KNN-inspired):
1. First, you input the dataset from Huggingface or a txt corpus.
2. Next, token embeddings are learned (in this implementation, token embeddings from scratch were used, but it's one of those things that some people will benefit more than others from different approaches, so I encourage you to modify this step as you see fit).
3. Then, the classifier looks through the data unsupervised, discovering n_C categories on its own. It does this by putting every example/sample, the entire thing, on the plane and applying K-Means clustering once. Where the example/sample is put is the mean embedding of every token inside the sentence.
4. It decides which category an example/sample is in by seeing how close it is to each category via cosine similarity in the embedding space. The logits' signs are flipped, and softmax is applied to get the confidence distribution.
5. An example/sample is considered in a category if it's above t_C. If it's not in any category, it goes to all experts.
6. t_C's baseline value is decided by the user, but a logistic regression model (same deal with the token embeddings) scores the complexity of the input. The equation is this: t_C = t_C_base * (1 - log(1 + k * c_S) / log(1 + k))
8. After training, the classifier is left alone and never touched again.
#### Experts (LSTM):
1. The training data is run through the trained classifier, and it determines which category each example/sample is in.
2. Each expert's training data is only one specific category, so there are n_C experts.
3. If the classifier thinks an example/sample belongs to multiple categories, each expert/category in that list gets it for training.
4. All the experts share a vocab and embeddings with the classifier.
5. The loss function is unique. Instead of next-token prediction, a RAG-style thing checks if what the expert produced is coherent and factual via semantic similarity. The RAG dataset for consulting is your training dataset.
6. Each expert is trained by minimizing loss.
#### Summarizer (LSTM):
1. Trained autoregressively like usual, shares a vocab and embeddings with the classifier and summarizer.
2. More than just a summarizer, these are the things it does:
- Receives every expert’s outputs.
- Resolves contradictions.
- Understands abstract instructions and can put them into human-understandable words.
- Detects intent via embeddings.
- Makes the final response coherent and keeps all the important and fine details the experts spat out.
3. Because a summarizer itself might not be good enough for coding in itself, we LoRA fine-tuned the summarizer to get good at translating abstract text into code. Based on whether the summarizer detects coding intent, the LoRA version is used.
### Inference Steps:
#### Classifier (KNN-inspired):
1. Your input is run through the classifier, and it detects which categories. All edge cases are handled through these 3 checks:
- If it fits one or multiple categories, it's routed to those experts.
- If it fits none, all experts engage.
- If two categories are within 0.1 of each other in the softmax scores, expert cross-talk is allowed.
2. Yeah, it has very few steps.
#### Experts (LSTM):
1. First, the inputs are run through the respective experts.
2. Then, the summarizer consolidates everything the experts say, explained later.
3. The cross-talk enabled by the close categories is very simple. The expert produces a response, and that raw response is fed directly into the other expert, or experts if it's 3 categories or more. Each expert has n_T turns to cross-talk with each other. To make up for the last expert not having anyone to respond to it, it produces a response twice.
#### Summarizer (LSTM):
1. When all expert outputs are gathered, the summarizer grounds them and gives a final response, keeping everything the experts wanted while also elaborating.
2. If the summarizer detects that the experts disagree or have gone off track via the intent mechanism, it ignores everything and asks for clarification.
## Shortcomings:
- Can't train as you chat because experts might diverge from categories due to blurry category lines.
- Experts can't inherit knowledge from each other during training, although it is the point.
- Semantic clustering isn't always precise when handling in-between-category inputs.
## Weaknesses:
- Due to limitations with money, large-scale training has not been tested. This isn't a weakness per se, but it's worth mentioning.
- Very dependent on the summarizer.
- Training is super inefficient.
- Fine-tuning overall is hard.
- Very sensitive hyperparameters.
## Strengths:
- Very modular, plug-and-play experts if you fine-tune the classifier with LoRA.
- Inference is super efficient.
- Extreme specialization in domains.
- Summarization, although a big bottleneck, is the most helpful part when done right.
- Experts are easy to train.
## Glossary:
- KNN: Looks at the input then looks at the closest real-life samples, as the entire training dataset is stored with the classifier.
- K-Means clustering: A simple grouping algorithm. It looks at the examples/samples and groups them into categories by assigning examples/samples as centers and constantly adding more examples/samples from outside and shifting the center.
- LSTM: A neural network whose main objective is to remember what came earlier and handle long-range chats.
- n_C: Number of categories.
- n_T: Number of turns during expert cross-talk.
- t_C: Threshold value for selecting experts.
- t_C_base: The user-defined base threshold value.
- k: Scaling factor in the threshold equation.
- c_S: A value between 0 and 1 giving the complexity of the input, determined by the logistic regression model.
# One final note:
Thank you to anyone who spent their time reading this. Code implementation is coming soon!

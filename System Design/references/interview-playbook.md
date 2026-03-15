# Interview Playbook

Use this reference when the user wants a model answer, mock interview, or feedback rubric.

## Recommended Answer Shape

### 1. Clarify scope

Ask what exact product behavior matters:

- core user actions
- out-of-scope features
- target clients
- scale expectations
- latency or consistency requirements

Do not ask ten questions. Ask the two to four that materially change the design.

### 2. Define requirements

Split into:

- functional requirements
- non-functional requirements
- constraints and assumptions

### 3. Estimate

Use rough but plausible numbers:

- DAU / MAU
- requests per second
- peak factor
- storage growth
- bandwidth
- object sizes

Interviewers usually care more about whether the math informs the design than whether the numbers are exact.

### 4. Core design

State:

- main services
- storage choices
- request flow
- data flow
- APIs
- data model

### 5. Deep dive

Choose one or two areas to deepen:

- scaling hot keys
- feed generation
- message ordering
- consistency model
- partitioning strategy
- failure recovery

### 6. Tradeoffs

Explicitly say what you chose not to optimize first.

## Coaching Rubric

Score the answer on:

- Structure: Is the response ordered and easy to follow?
- Requirements: Were the important requirements discovered?
- Estimation: Did the user quantify scale enough to justify choices?
- Architecture: Are components appropriate and connected coherently?
- Data: Are APIs and storage choices plausible?
- Scale: Are bottlenecks identified with workable mitigations?
- Tradeoffs: Are alternatives and consequences explained?
- Communication: Does the user sound decisive and interview-ready?

## Common Failure Modes

- Jumping into microservices before clarifying requirements
- Naming technologies without explaining why
- Ignoring peak traffic
- Avoiding data model/API discussion
- Hand-waving consistency and failure behavior
- Overdesigning for global scale when the numbers do not require it
- Forgetting monitoring, rate limiting, abuse controls, or backpressure

## Feedback Style

When reviewing the user's answer:

1. Start with the biggest missing decision.
2. Point out the architectural consequence.
3. Offer a stronger interview phrasing.
4. Suggest one improvement for the next attempt.

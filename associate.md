# Claude Certified Associate, Foundations (CCAO-F)

60 items, 120 minutes (about 2 minutes each), scaled pass mark of 720 on a 100 to 1,000 scale. No coding. The questions describe a workplace task and a constraint, then ask for the best approach: which feature or model fits, what to check before sharing an output, or when a person must step in.

## Domains and weights

| # | Domain | Weight | About this many of 60 items |
|---|---|---|---|
| 1 | Prompting and Task Execution | 14% | 8 |
| 2 | Output Evaluation and Validation | 21% | 13 |
| 3 | Product and Model Selection | 12% | 7 |
| 4 | Workflow Integration and Solution Design | 16% | 10 |
| 5 | Configuration and Knowledge Management | 12% | 7 |
| 6 | Governance, Risk, and Responsible Use | 15% | 9 |
| 7 | Troubleshooting and Optimization | 10% | 6 |

Domains 2, 4 and 6 are just over half the exam. A gap in any of them costs the most.

## Objectives, condensed

**1. Prompting and Task Execution**
- Write clear prompts for business and technical tasks
- Break complex requests into steps
- Iterate a prompt to improve the output
- Match the prompting approach to the task: analysis, research, drafting, brainstorming

**2. Output Evaluation and Validation**
- Check outputs for accuracy and completeness
- Spot hallucinations, inconsistencies and bias
- Fact-check and validate claims
- Decide when human review is required
- Edit and adapt outputs for the audience
- Choose the output format: inline, artifact or structured data

**3. Product and Model Selection**
- Pick the right feature: chat, Projects, Research, artifacts
- Tell Haiku, Sonnet and Opus apart
- Trade off cost, speed and quality
- Manage context limits: when to restart, summarize or persist

**4. Workflow Integration and Solution Design**
- Use Claude to analyze requirements and use cases
- Use Claude for research, planning and process improvement
- Fit Claude into existing workflows, to augment or redesign them
- Explain Claude's value and limits to stakeholders

**5. Configuration and Knowledge Management**
- Set up Projects with instructions and knowledge
- Manage uploaded files and connectors
- Write good system-level instructions
- Keep configurations and knowledge up to date

**6. Governance, Risk, and Responsible Use**
- Tell appropriate from inappropriate use cases
- Apply data sensitivity, privacy and regulatory rules
- Follow your organization's AI policy

**7. Troubleshooting and Optimization**
- Diagnose underperforming prompts and poor outputs
- Adjust based on feedback and results
- Make workflows faster and more reliable

## Three practice questions

These are original questions written for these notes, in the exam's style.

**Q1.** A support team wants Claude to tag 20,000 short tickets a day into one of five categories. Accuracy on this simple task is already high with every model they tried. What should drive the model choice?

- A. Use the most capable model, because accuracy always improves with size
- B. Use the fastest, lowest-cost model that meets the accuracy bar
- C. Rotate models daily and keep the one with the best results
- D. Use extended thinking on every ticket

**Answer: B.** When quality is already sufficient, cost and speed decide. A smaller, faster model such as Haiku fits high-volume, simple classification. A ignores cost, C adds noise without a reason, D adds latency and cost the task does not need.

**Q2.** Claude drafts a client report and includes a market-size figure with a named source. The report goes to the client tomorrow. What is the best next step?

- A. Send it, since Claude named a source
- B. Ask Claude whether it is confident in the figure
- C. Check the figure against the original source before sending
- D. Remove the source name and keep the figure

**Answer: C.** Named sources can still be wrong or invented, and the report is client-facing. Verify against the primary source. B asks the model to grade itself, D hides the problem.

**Q3.** A team pastes the same brand style guide into every new chat, and outputs still drift when someone forgets. What fixes this best?

- A. Ask everyone to bookmark the style guide
- B. Put the style guide in a shared Project's instructions or knowledge
- C. Switch to a larger model
- D. Shorten the style guide so it is easier to paste

**Answer: B.** Project instructions and knowledge apply to every chat in the Project, so nobody has to remember. The other options keep the manual step that causes the drift.

## Practice

- [Free 25-question CCAO-F diagnostic, scored by domain](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=study-notes&utm_campaign=claude-cert-notes)
- [All four Claude certifications compared](https://prepclubs.com/claude-certification?utm_source=github&utm_medium=study-notes&utm_campaign=claude-cert-notes)

Source: Anthropic, Claude Certified Associate, Foundations exam guide.

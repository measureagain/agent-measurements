# I ran the same AI code review four times. It was not the same review.

I work with coding agents all day. Reviews, mostly. One model reviews the code, another one reviews it again, and I read what comes out.

At some point I noticed something that bothered me. When two models found different problems, I took it as "this model is better at this". But I had never asked the obvious question. If I run the same model again, with the same prompt, on the same code, do I get the same review?

I didn't know. And every comparison video I found runs each model once and picks a winner. So I measured it.

## What I did

I took a public pull request, so anyone can check me: [number 3896 of Click](https://github.com/pallets/click/pull/3896). It adds one parameter to a testing helper. 178 lines, 5 files.

Two reviewers, Codex (`gpt-6.2-sol`, medium effort) and Claude Sonnet 5.5. Same prompt for both. Four runs each. Nothing changed between runs.

Then the findings got matched one by one. Same problem in the same place counts as the same finding. To be honest about it, an agent did the matching, not me. I read the result.

## What came back

| | Codex | Sonnet |
|---|---|---|
| Different findings over four runs | 8 | 12 |
| In all four runs | 5 | 2 |
| In three runs | 2 | 1 |
| In two runs | 1 | 3 |
| In one run only | 0 | 6 |
| Time per run | about 105 s | about 42 s |

Codex was stable here. Five of its eight findings came back every single time.

Sonnet was not. Half of what it found showed up in one run and never again. If you ran Sonnet once, you saw 5 to 7 findings out of 12. Run it again and you get a different 5 to 7.

And the thing is, this is not "Codex is stable, Sonnet is not". On a private project of mine, the same Codex setup repeated only 10 of 21 findings. So it depends on the model and on the code. From one run you can't know which case you are in.

## The two models did not find the same things

16 different findings in total. Only 4 came from both models. Codex alone had 4. Sonnet alone had 8.

The most serious one came only from Codex, in all four runs. Before the change, if you passed `context_settings` to `invoke`, it went through to your own custom command. After the change, `invoke` keeps it for itself, and your command silently gets its default. Codex says it reproduced it on both commits. I did not reproduce it myself.

## Were the findings real? This is where it got weird

Agents produce far more than a person can review. I was not going to check 16 findings by hand. So I asked two other models, Codex and Claude Opus 5.5, the same question about each finding. Is this a real problem of this change?

- Both said yes: 7.
- Both said no: 1.
- They disagreed: 8.

Half. And they did not disagree about the code. Both described the same lines. They disagreed about what "real problem" means. For Codex, a true statement is a finding. For Opus, it only counts if the change broke something or broke a rule the project wrote down.

One example. A docs example passes whether the new setting is applied or not. True. But that example was already weak before this change. Is that a finding of this pull request? One model says yes, the other says no. I think both have a point.

By the strict count, 6 of Codex's 8 findings hold, and 4 of Sonnet's 12.

## What I do differently now

1. One review run is a sample. Before I trust "no findings", I run it twice.
2. A second model gives me more than a second run of the same one. Only 4 of 16 overlapped.
3. More findings is not better. Sonnet reported more, and fewer of them held.
4. Before asking a model "is this finding real?", I say what real means. If not, I'm measuring the judge and not the finding.

## What this does not prove

It is one pull request, one project and one prompt. No person verified the findings, only models. And the two reviewers did not work under the same conditions: Sonnet said in three of its four reports that it could not run code, and Codex did run code.

So take it as one measurement, not as a ranking. If you think I did it wrong, tell me how. The prompt, the eight reports and the two checks are in this folder.

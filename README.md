# PRUS Human Validation Site — Component-First Alternative B

This repository contains a third GitHub Pages website for post-level human validation of Pre-Release User Speculation (PRUS). It uses the same blinded 500-post sample as the two existing sites, but does not ask coders to make a direct PRUS judgment. Instead, it validates the observable components used by the machine-labeling pipeline and derives PRUS after coding.

## Validation Unit

The unit presented to coders is a Steam topic-root post, represented by the post title and the captured post body/excerpt retained in the analysis corpus. This matches the unit at which sentence-level PRUS evidence is aggregated in the study. The website does not ask coders to judge isolated sentences.

For each post, coders answer three ordered questions: (1) whether uncertainty is expressed; (2) whether that uncertainty applies to an identifiable claim, explanation, prediction, interpretation, theory, hypothetical, or unresolved alternative; and (3) which product topic domains the uncertain idea concerns. The available domains are content, performance, and requirements/access, and multiple domains may be selected. Coders can instead record that the uncertain proposition concerns no qualifying product topic domain.

The application derives a post-level PRUS result only when uncertainty is present, an uncertain proposition is recoverable, and at least one qualifying product topic domain is selected. A question mark alone does not satisfy the uncertainty component, while hopes, suggestions, assumptions, conditionals, and implied possibilities can provide uncertainty framing. The pre-release and user-to-user conditions are fixed by the sample. The secure backend recalculates the derived result, so a browser cannot save a value inconsistent with the component answers.

The alternative deployment is intentionally separate from the main validation site. Its distinct sample version, browser-storage prefix, and private response namespace allow instruction effects to be compared without changing the live main codebook or overwriting its responses.

## Files

- `index.html`: validation interface
- `styles.css`: responsive site styling
- `app.js`: browser persistence, secure checkpoints, resume, and response export
- `config.js`: secure response endpoint and checkpoint interval
- `worker/`: Cloudflare Worker that validates submissions and writes them to the private GitHub response repository
- `data/sample_posts.json`: blinded 500-post validation sample used by the site

Model labels, class allocation, sampling strata, selection thresholds, randomization details, and diagnostic materials are retained outside the public application repository. The browser receives only the post information required to complete the exercise.

The captured body sometimes ends with an ellipsis because the source Steam discussion corpus retained a topic summary/excerpt rather than retrieving a later full thread page. The validation interface reproduces the same captured text that entered the sentence-level analysis, preserving measurement alignment.

## Local Use

```bash
python3 -m http.server 8787
```

Open `http://127.0.0.1:8787`.

## Persistence and Secure Collection

The site stores a browser copy of participant progress in `localStorage` and sends secure checkpoints to a Cloudflare Worker. The Worker validates each submission and commits the latest post-validation record to the private `daakrofi/prus-human-validation-responses` repository.

The first component-first instruction round remains stored under `responses/post-validation-component-first-v1/`. Responses to the revised domain-gated codebook are stored under `responses/post-validation-component-first-v2/`. This keeps instruction rounds separate from one another, the main direct-PRUS exercise, the cue-and-scope hybrid exercise, and the superseded sentence-level exercise.

Participant email addresses are normalized and hashed before the GitHub path is created. Names, email addresses, and telephone numbers do not appear in filenames or commit messages. They remain inside the response JSON in the private repository.

The site creates a remote checkpoint every 25 completed posts, when the participant selects **Save and Exit**, and after all 500 posts have been completed. Browser-side checkpoint requests are serialized and coalesced. The Worker retries transient GitHub conflicts and prevents an older checkpoint from replacing a more advanced participant record.

## Response Export

At completion, coders can download CSV or JSON backups containing participant details, post metadata and displayed text, the uncertainty-cue judgment, the uncertain-proposition judgment, all selected product topic domains, the no-qualifying-domain judgment, the derived PRUS result, and the answer timestamp. Model labels are joined only after collection through the private audit file.

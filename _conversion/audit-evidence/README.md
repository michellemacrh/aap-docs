# AWS pre-check audit evidence

This directory retains the inputs and focused verification results supporting [the audit](../precheck-audit.md). These experiments supplement the 2026-11-05 pre-check; they do not establish readiness after the writer's pending changes.

## Evidence inventory

- [verification.json](verification.json): structured map, resource-syntax, link, filename, image, and chunk results, exact executed commands, tool versions, and input hashes.
- [audit-verification.log](../logs/audit-verification.log): consolidated nonempty supplemental command output. Each section identifies its original filename; trailing console whitespace is trimmed, with diagnostic messages preserved. The nonempty original pre-check logs and the AWS-specific scope/conditional scans remain in `../logs/`.
- [inputs.json](inputs.json): unchanged contents of the 17 generated AsciiDoc maps and the combined attributes used by the accepted comparison, bundled to keep historical inputs out of the active source tree. These were generated conversion inputs at repository root, not authored product changes. The original CSV snapshot is unavailable; these are historical inputs, not a current JTBD export.

The input maps retain their original repository-root-relative includes. To reproduce the historical run, restore the `maps` entries in `inputs.json` as files at the root of a disposable checkout of the recorded revision, and write `attributes.content` to a file supplied with `-p`; preserve repository symlinks. Subsequent source fixes or regenerated maps require fresh validation.

Empty logs were removed rather than filled with synthetic output. Successful commands listed below emitted no stdout/stderr; their successful exits and subsequent file/anchor assertions were observed during the focused tests and are recorded here and in `verification.json`. Original temporary execution paths in the JSON are historical provenance.

## Map nesting

Revision: `dce13e14d271e4dce0fb01f63a32496076db9039`, source base `d7307c91c2`. Converter image ID: `aafcc2d86d6bf7fadf05a52ddf71d6a43b2a169c5b0f4ae90834d3710ca1446b`. DITA-OT: 4.4.1.

Compared `convert -rC -p attributes.adoc aap-service-aws-navigation.adoc` with `convert -ArC -p attributes.adoc aap-service-aws-navigation.adoc` on isolated unchanged inputs. Invalid-mapref warnings fell 85 → 0; map-model errors 10 → 0. All 99 selected source paths remain reachable exactly once, all 14 assembly introductions remain, and 100 topics are converted. The DNS dependency is the one converted topic outside navigation. The uncleaned corrected build still contains the independent image and chunk issues documented in the audit.

## Resources and cleanup fallback

A role-marked `.Additional resources` block produces related links for both concepts and references. A heading alone works in concepts but remains a body section in references. Four existing converted topics were cleaned with `--prune-ids`, `--prune-xrefs`, and `--xref-dir .`; all three internal links rendered with valid destination anchors. The focused cleanup and DITA-OT XHTML build both exited 0 with no console output. This is a fallback for unchanged source; the audit prefers explicit source link destinations.

## Organized output verification

The test uses accepted existing `-A` DITA, organized by category, with standard filename, ID, image, xref, and map-path cleanup. The rename logic was exercised with plain file moves instead of Git bookkeeping; no commit was made. All six image assets were copied. The known writer image defect was isolated by removing the invalid generated `width` only in the disposable test copy, not by changing product source.

Cleaned output still logged 8 chunk-move failures and 10 each of DOTX031E/DOTX026W. Removing `chunk` from the 10 maprefs, retaining all job-topic chunking and hierarchy, produced a DITA-OT XHTML build that exited 0 with no console output. All 99 selected topics remained reachable, all topic IDs in the 10 chunked jobs appeared in their job pages, and all 194 rendered local link/image references resolved; six images rendered. Assertions found no missing files or anchors. No topic bytes changed between those two build variants.

The standard rename step changed `con-saas-networking-with-overlapping-CIDR-blocks.dita` to its lowercase equivalent, updated its one map reference, and left no uppercase filename references. The topic remained reachable. The source filename is intentionally unchanged; renaming remains a conversion-engineer task.

## Source link verification

In isolated copies of four real source topics, the three ID-only links were changed to explicit `.adoc#id` targets. All three generated DITA links immediately resolved to existing files and IDs without cleanup. The focused DITA-OT XHTML build exited 0 with no console output; rendered href and destination-anchor assertions passed for all three links. A focused four-topic AsciiDoc assembly also built with exit 0 and no console output; all three links resolved to in-book anchors. The complete updated AWS book and final organized navigation still require validation after the writer's edits.

## Source map verification

Only the 10 redundant chunk options were removed from includes in the five generated AsciiDoc category maps. Focused nonrecursive conversion produced no chunked maprefs. Canonical XML comparison confirmed that all five outputs exactly match the previously verified `-A` DITA maps with only mapref chunk attributes removed. The job source maps were byte-for-byte unchanged. The correction belongs in generated-map preparation before conversion; a lasting generation/preparation implementation remains pending.

## Removed empty conditional log

The original tracked `conditionals.log` was empty. Its absence after cleanup is not a clean conditional result. The retained nonempty `aws-conditionals.log` inventories the AWS scan; the audit also records the 13 paired `ifndef` guards found by source review and the missing business-continuity context restoration.

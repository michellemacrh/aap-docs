# AAP Service on AWS pre-check audit

**Run:** 2026-11-05 · `conversion-aap-service-aws` at `dce13e14d2`; source content matches the local `upstream/aap-clouds-latest` ref at `d7307c91c2` (remote not refreshed in this review) · CSV map mode, prefix `aap-service-aws` · versionless product.

**Readiness:** Not ready for conversion. The writer must resolve the source fixes and content decisions below on `aap-clouds-latest`, then notify the conversion engineer of the source revision and any JTBD export changes. The conversion engineer must incorporate those changes and rerun the pre-check using the already verified `-A` option. The map-nesting fix is verified; overall readiness still depends on resolving and checking the remaining findings.

## Writer source fixes — Vale, source structure, and DITA cleanup

### DNS validation commands use unsupported block delimiters

Apply this source fix if the writer retains the DNS topic in the migration; settle its scope decision below first.

[`vale.log`](logs/vale.log) flags the `.Test Local Resolution` block title at line 112 and four thematic-break markers at lines 114, 117, 123, and 131 in `aap-clouds/topics/con-saas-dns-resolution-requirements.adoc`. [`conversion.log`](logs/conversion.log) reports two thematic-break warnings. The `---` pairs surround shell commands but are not code-block delimiters. Replace each pair with a `[source,bash]` block using `----` delimiters, and replace `.Test Local Resolution` with `=== Test local resolution` under the existing `== Validation` heading. Confirm the pre-check no longer flags the title or thematic breaks and the generated DITA retains both command blocks.

**Owner:** Writer · **Timing:** Before conversion — source fix

### Keep validation actions and request templates inside valid task structure

[`vale.log`](logs/vale.log), lines 30–37, and [`conversion.log`](logs/conversion.log), lines 71–78, identify unsupported content in four procedures. The converter reports `Unsupported title 'Validation' found, skipping...` and `Non-list elements found in steps, skipping...`; source content can be lost.

**Fix these files under `aap-clouds/topics/`:**

- In `proc-saas-custom-dom-with-awsprivatelink.adoc`, lines 52–67, remove the unsupported `.Validation` block title and put its introductory text and checks into the existing ordered `.Procedure` list as validation steps, with continuation markers for attached paragraphs/notes. Do the same in `proc-saas-custom-dom-without-awsprivatelink.adoc`, lines 57–70. If validation is split into a separate procedure instead, update the JTBD export to include it.
- In `proc-saas-disconnect-egress-privatelink.adoc`, lines 31–54, and `proc-saas-disconnect-ingress-privatelink.adoc`, lines 26–50, the flagged content is the standalone support-request template after the step list. Move each template to the first “Submit … case” step and attach its title and literal block with list continuation markers (`+`). Keep the “Verify connectivity” checks attached to their ordered step; nested checks are not themselves proof of invalid task structure.

**Verify:** After a pre-check rerun, inspect all four generated tasks and confirm that validation actions, connectivity checks, and the full support-request templates survive, with no unsupported-title/non-list warnings for these structures.

**Owner:** Writer · **Timing:** Before conversion — source fix

### Add or shorten source abstracts

[`vale.log`](logs/vale.log), line 1, and [`dita-cleanup.log`](logs/dita-cleanup.log), lines 5–7, 15, and 18–19, report an unmarked introduction and oversized abstracts. In `aap-clouds/stories/assembly-saas-business-continuity.adoc`, add a concise `[role="_abstract"]` description after the title; keep the longer introductory explanation as body text. Five resolved short descriptions exceed 300 characters in this run:

- `aap-clouds/topics/con-saas-networking-with-overlapping-CIDR-blocks.adoc` — 408 characters
- `aap-clouds/topics/con-saas-privatelink-config-types.adoc` — 305 characters
- `aap-clouds/topics/con-saas-automation-mesh.adoc` — 312 characters
- `aap-clouds/topics/ref-saas-guidance-parameters.adoc` — 311 characters
- `aap-clouds/topics/ref-saas-notification-timeline.adoc` — 352 characters

After moving abstract links as directed below, shorten any descriptions still over 300 characters. Measure the resolved text, including expanded product names, then rerun the pre-check. Moving the link out of the PrivateLink configuration-types abstract might already bring it within the limit.

**Owner:** Writer · **Timing:** Before conversion — source fix

### Confirm the PrivateLink configuration topic type before moving its links

`aap-clouds/topics/con-saas-config-aws-privatelink.adoc` declares `CONCEPT` at line 1, but lines 15–19 introduce numbered setup steps. [`vale.log`](logs/vale.log), line 12, and [`dita-cleanup.log`](logs/dita-cleanup.log), lines 31–32, flag its link placement under the current concept type.

**Decision for the writer:** Is this a setup procedure or an explanation of the SRE-assisted setup process? For a procedure, set `_mod-docs-content-type` to `PROCEDURE`, add a `.Procedure` list of reader actions, and move the statement describing SRE work into the appropriate step/result; preserve the topic ID. For a concept, rewrite the numbered actions as explanatory prose and move the final “Enabling AWS PrivateLink connectivity” link into the role-marked `.Additional resources` block described below. If the file is renamed, update its source includes and JTBD export.

The conversion engineer must reassess link placement against the confirmed type; a concept-only rule must not remove links needed within procedure steps.

**Owner:** Writer (type decision and source edit); conversion engineer (validate output for the chosen type) · **Timing:** Before conversion — confirm type, then rerun the applicable checks

### Move links from concept bodies to related resources

[`dita-cleanup.log`](logs/dita-cleanup.log) reports `Link in concept topic`: these links remain in concept bodies or abstracts rather than DITA related links. [`vale.log`](logs/vale.log) also flags many of them as `AsciiDocDITA.ConceptLink`; cleanup finds additional links expanded from attributes. Move each indicated link into a link-only resources block and keep the surrounding explanation in the body. Use this converter-recognized form (substitute the existing destinations and link text):

```adoc
[role="_additional-resources"]
.Additional resources
* link:URL[Link text]
```

The [focused syntax test](audit-evidence/README.md#resources-and-cleanup-fallback) confirms this creates DITA `<related-links>` for both concepts and references. A heading-only resources section also works in a concept; use the block form here for consistency across source types. Preserve destinations; the cleanup log records the resolved URLs.

Edit these files under `aap-clouds/topics/` (line numbers refer to this run's source):

| File | Lines | Links to move |
|---|---|---|
| `con-aap-notification-feed.adoc` | 10 | Notification feed |
| `con-saas-automation.adoc` | 12–13 | Deploying Automation Mesh blog |
| `con-saas-automation-mesh.adoc` | 18, 24 | Operator mesh guide; ingress configuration |
| `con-saas-backup-and-disaster-recovery.adoc` | 54 | Existing customers |
| `con-saas-connectivity.adoc` | 16 | Support link expanded from `{CustomerSupport}` |
| `con-saas-customer-access.adoc` | 16, 20 | Support link expanded from `{CustomerSupport}`; Custom Domain |
| `con-saas-deployment-self-service.adoc` | 23 | Support ticket |
| `con-saas-dns-resolution-requirements.adoc` (if retained) | 147, 168–171 | Support; custom domain setup; enabling PrivateLink; AWS PrivateLink docs |
| `con-saas-how-private-link-works.adoc` | 10 | Amazon Virtual Private Cloud docs |
| `con-saas-identity-and-access-management.adoc` | 10 | Guide expanded from `{LinkCentralAuth}` |
| `con-saas-privatelink-config-types.adoc` | 10 | PrivateLink connectivity guide |
| `con-saas-regions.adoc` | 10 | Multi-region Active/Passive infrastructure |
| `con-saas-shape.adoc` | 13–14 | Operator mesh guide |
| `con-saas-sla.adoc` | 7 | Enterprise Agreement appendices (currently in the abstract) |
| `con-saas-updates-and-maintenance.adoc` | 12 | Updating Receptors in the mesh guide |

In `con-saas-dns-resolution-requirements.adoc`, replace the trailing `[role="_related-topics"]` marker at line 168 with `[role="_additional-resources"]` followed immediately by `.Additional resources`, and put the support link from line 147 there too. Make the AWS documentation item at line 171 a link-only bullet, for example `* link:https://docs.aws.amazon.com/vpc/latest/privatelink/[AWS PrivateLink documentation]`. Keep the ordered help actions meaningful without the inline support link. `con-aap-notification-feed.adoc` is the same shared source selected by both AWS and Azure. Coordinate that edit with the Azure writer on `aap-clouds-latest`; both conversion branches must incorporate it. Change link placement in each topic rather than changing shared attribute definitions. Rerun the pre-check; acceptance is no concept-link findings in either Vale or DITA cleanup for the listed files, with every original destination preserved.

**Owner:** Writer · **Timing:** Before conversion — source fix

### Move links in three assembly introduction topics

[`dita-cleanup.log`](logs/dita-cleanup.log) reports links in the introductory concept topics generated from `aap-clouds/stories/assembly-saas-intro.adoc`, `aap-clouds/stories/assembly-saas-post-install-config.adoc`, and `aap-clouds/stories/assembly-saas-support.adoc`. Each source declares `ASSEMBLY`; the verified `-A` invocation retains its introduction as a concept parent topic. Child topics must remain explicit in the agreed CSV scope. Keep these introductions in the assembled output. In `assembly-saas-intro.adoc`, line 11, move the AWS Marketplace link out of the abstract into a `[role="_additional-resources"]` / `.Additional resources` link-only block. In `assembly-saas-post-install-config.adoc`, lines 14–15, move the Automation Mesh link there. In `assembly-saas-support.adoc`, lines 10–12, move the Red Hat Customer Portal link out of the abstract and put it and the Production Support Terms link there. Place each resources block before the first child include (or before the context restoration in the childless intro assembly). Keep link-free introductory sentences and preserve the existing child includes and explicit CSV topic entries. Preserve each destination and confirm the pre-check no longer reports concept links for these sources.

**Owner:** Writer · **Timing:** Before conversion — source fix

### Make the metering resources a recognized, link-only block

[`vale.log`](logs/vale.log), lines 38–40, reports `AsciiDocDITA.RelatedLinks` for `aap-clouds/topics/ref-saas-infrastructure-metering.adoc`, lines 100–102. These entries contain navigation instructions and unlinked references. The generated REFERENCE topic also keeps its `== Additional resources` heading as a body section, with no `<related-links>`. The [focused syntax test](audit-evidence/README.md#resources-and-cleanup-fallback) confirms a heading alone stays in a reference body; the role-marked block used in the Azure audit produces `<related-links>`.

**Fix:** In this source file, move the line 100 Subscription Usage navigation instructions into `== Monitor infrastructure use` (lines 44–49), merging duplicate guidance. Keep the cost-management explanation in that body section. Replace the heading at line 98 with `[role="_additional-resources"]` immediately followed by `.Additional resources`. Replace lines 100–102 with link-only bullets for `https://console.redhat.com`, the intended AWS Cost Management documentation, and the intended Service on AWS Marketplace listing. The writer must supply the two specific destinations currently mentioned without URLs; do not invent them or leave prose-only bullets.

**Acceptance:** The generated reference contains those destinations in `<related-links>`, the navigation/cost guidance remains in its body, and Vale no longer reports `RelatedLinks` for these entries.

**Owner:** Writer · **Timing:** Before conversion — correct the block structure and content

### Keep image alt text in the alt attribute

In `aap-clouds/stories/assembly-saas-control-plane.adoc`, line 18, the `image::AWS_tenant_isolation.png[...]` description contains an unquoted comma. The 2026-11-06 test independently confirms that the description after the comma becomes the generated image's `width` attribute, producing `DOTJ088E`: `Attribute value "however the diagram illustrated ..." of type NMTOKEN must be a name token.` See the [uncleaned `-A` build output](logs/audit-verification.log). This error remains after the map-nesting fix.

**Writer source fix:** In `aap-clouds/stories/assembly-saas-control-plane.adoc`, line 18, rewrite the macro with explicit named alt text, for example `image::AWS_tenant_isolation.png[alt="Two customer deployments isolated in separate AWS infrastructure."]`. Confirm the description matches the diagram. Correct the AsciiDoc macro before conversion so the invalid width is not generated again.

**Conversion-engineer verification:** After incorporating the writer's change, verify the generated `<alt>`, absence of text in `width`, and absence of `DOTJ088E` in a retained DITA-OT build log. Image-path normalization and copying the assets remain separate conversion tasks.

**Owner:** Writer (correct the image macro); conversion engineer (verify generated image attributes and build) · **Timing:** Before conversion — source fix; verify during the rehearsal

### Give the three internal source links explicit file-and-ID targets

The original [`conversion.log`](logs/conversion.log) contains five `Possible invalid reference` warnings for three target IDs; [`missing-refs.log`](logs/missing-refs.log) marks all three `SAFE`. The destinations exist, but the source uses ID-only cross-topic xrefs. Standalone topic conversion leaves those as fragment-only links that need target lookup.

**Preferred fix before conversion:** In the following files under `aap-clouds/topics/`, replace only the xref target shown; preserve its existing link text and the target topic ID:

- `proc-saas-standard-ingress-configuration.adoc:8`: `xref:ref-saas-network-dns-routing[...]` → `xref:ref-saas-network-dns-routing.adoc#ref-saas-network-dns-routing[...]`.
- `ref-saas-exe-plane-strategy.adoc:12`: `xref:ref-saas-infrastructure-metering[...]` → `xref:ref-saas-infrastructure-metering.adoc#ref-saas-infrastructure-metering[...]`.
- `ref-saas-network-dns-routing.adoc:9`: `xref:proc-saas-standard-ingress-configuration[...]` → `xref:proc-saas-standard-ingress-configuration.adoc#proc-saas-standard-ingress-configuration[...]`.

**Verified:** A focused test of the four real source topics produced all three file-and-ID DITA targets without ID/xref cleanup, and their rendered destinations and anchors resolve. The same source links resolve to in-book anchors in a focused four-topic AsciiDoc assembly. See the [source-fix results](audit-evidence/verification.json), [conversion output](logs/audit-verification.log), and [DITA/AsciiDoc rendered-link verification](audit-evidence/README.md#source-link-verification). Validate the updated full AWS book and final navigation after incorporating the writer's edits. Ordinary relative-path updates after topics move into final category directories still belong to conversion packaging.

The earlier [cleanup-only test](audit-evidence/README.md#resources-and-cleanup-fallback) remains a verified fallback for unchanged source. Prefer making the destinations explicit in source; a product-specific target-lookup workaround is unnecessary for these three links. This recommendation supersedes the earlier conversion-only disposition, which established that cleanup worked without testing a source alternative.

**Owner:** Writer (source xref edits); conversion engineer (incorporate source, update ordinary relative paths after organization, and verify final links) · **Timing:** Before conversion — update source links and check the assembled book; during conversion — verify final paths and anchors

## Writer scope and conditional decisions

### Confirm the DNS topic's migration scope

[`aws-scope-audit.log`](logs/aws-scope-audit.log), lines 6–14, confirms all 99 CSV paths are included and identifies `aap-clouds/topics/con-saas-dns-resolution-requirements.adoc` as an additional source dependency. In `aap-clouds/stories/assembly-saas-control-plane.adoc`, line 67 actively includes that topic; generated `control-plane.adoc` has no direct row for it. The verified `-A` test converts this topic but leaves it outside the navigation because its assembly is now a topic rather than a map that exposes its internal includes. This is the sole converted topic outside the corrected AWS navigation; see the [verification summary](audit-evidence/README.md#map-nesting). It needs an explicit scope decision.

**Decision for the writer:** Should the DNS topic appear under the Control plane job? If yes, add its file row to that job in the authoritative JTBD structure and the derived `AWS csv2map export`, preserving the category grouping and agreed chunking. If intentionally excluded, record that decision and ensure in-scope DNS guidance and links do not depend on the excluded topic. Apply its block-delimiter and link-placement source fixes above if it remains in scope. The conversion engineer must regenerate the maps and verify the agreed result. Do not edit the generated `control-plane.adoc` as the lasting fix.

Only this active topic include is outside the 99 direct paths in the retained generated maps. The `ref-saas-privatelink-failover-limitations.adoc` and `con-saas-provisioning.adoc` includes in the multiregion and setup assemblies are commented out; they are not active scope dependencies.

**Owner:** Writer (scope decision and JTBD updates); conversion engineer (map regeneration and verification) · **Timing:** Before conversion — confirm scope and update the export if included

### Remove avoidable context guards and correct business-continuity context handling

[`aws-conditionals.log`](logs/aws-conditionals.log) scanned 116 included files and lists 27 `ifdef` directives across 14 assemblies; source review also finds 13 paired `ifndef` directives, making 40 guards in this snapshot. These save/restore context attributes rather than select product content. Remove avoidable guards where fixed invocation context permits it, preserving required context; their purpose alone does not establish that every guard must stay.

**Source assessment and fix:** For these files under `aap-clouds/stories/`, establish the incoming context required by each caller and included topic before changing the guards:

- `assembly-saas-aap-best-practices.adoc`
- `assembly-saas-account-management.adoc`
- `assembly-saas-business-continuity.adoc`
- `assembly-saas-control-plane.adoc`
- `assembly-saas-execution-plane.adoc`
- `assembly-saas-intro.adoc`
- `assembly-saas-post-deloyment-configuration.adoc`
- `assembly-saas-post-install-config.adoc`
- `assembly-saas-private-link.adoc`
- `assembly-saas-pull-push.adoc`
- `assembly-saas-service-definition.adoc`
- `assembly-saas-set-up.adoc`
- `assembly-saas-subscription-lifecycle.adoc`
- `assembly-saas-support.adoc`

The current AWS book, `aap-clouds/titles/saas-aws/stories.adoc`, includes all 14 assemblies. The conversion input also lists them directly in its generated maps. Where a fixed invocation context permits it, replace conditional save/restore logic with explicit assignments or resets suitable for those callers. Preserve the context-dependent notification-feed ID (`con-aap-notifications-feed_{context}`) and any authored references. Do not delete every guard or apply one unconditional replacement to all assemblies without checking those uses. If caller-dependent behavior must remain, record the specific callers/context values that require it and agree preprocessing with the conversion engineer.

**Business-continuity defect:** In `assembly-saas-business-continuity.adoc`, line 3 saves the incoming context and line 5 sets `saas-business-continuity`, but the file ends after line 58 without restoring or clearing it. Correct this while simplifying the context handling so later book includes do not inherit that value. If the writer establishes that its guards are necessary and retains them, complete the sequence at the end with the following fallback, already used in `assembly-saas-control-plane.adoc`:

```adoc
ifdef::parent-context[:context: {parent-context}]
ifndef::parent-context[:!context:]
```

**Acceptance:** Record any necessary retained guards and their conversion handling; the conversion engineer checks the current book and generated IDs/references on the updated source. The existing 40-guard count is an inventory, not evidence that all 40 can safely be removed. No universal context replacement has been tested.

**Owner:** Writer (remove avoidable guards, correct business-continuity handling, and document necessary exceptions); conversion engineer (agree preprocessing for exceptions and check IDs/references) · **Timing:** Before conversion — apply source corrections and record exceptions; during conversion — apply agreed preprocessing

## Conversion-engineer preparation and output handling

### Use the verified converter option to correct generated map nesting

[`conversion.log`](logs/conversion.log), lines 133–226, reports 85 `invalid mapref child element` warnings across 10 generated job maps. With `convert -rC`, a source assembly becomes a `<mapref>` whose nested CSV topic references violate the DITA map content model. For example, `account-management.adoc` lists its assembly and six child topics. The assembly also includes those topics; that expansion produces the duplicate-ID warnings in [`asciidoctor.log`](logs/asciidoctor.log). Do not rename source IDs to work around generated-map expansion.

**Verified fix:** Add `-A`: use `convert -ArC` so assemblies become topics that can contain the sheet-listed child topic references. The 2026-11-06 isolated AWS comparison used unchanged source, combined AWS attributes, and the 17 retained generated AsciiDoc maps ([input snapshot](audit-evidence/README.md#evidence-inventory)), preserving repository symlinks. Invalid-mapref warnings fell from **85 to zero**, and DITA-OT map content-model errors from **10 to zero**. All 99 directly selected source paths are reachable exactly once; all 14 assembly titles and their existing short descriptions are retained. Business-continuity still lacks its short description, as reported above. All corrected map/topic references resolve, XML is well formed, and no corrected job map repeats a topic href. See the [verification summary](audit-evidence/README.md#map-nesting), [detailed results](audit-evidence/verification.json), [baseline conversion](logs/audit-verification.log), and [corrected conversion](logs/audit-verification.log).

**Conversion action:** Configure both the AWS pre-check dry run and conversion invocation to use `-A`; the current generic wrappers use `convert -rC`. Preserve the CSV hierarchy and source types. Direct `csv2map --ditamap` replacement and include suppression are not required to fix this nesting defect. The earlier partial direct-map trial is superseded for this recommendation and did not establish full readiness.

**Status:** The map-nesting fix is verified on the current AWS source. The remaining work for this finding is to configure the AWS invocations to use that option. There is no writer action for the nesting defect.

The test build also reports independent image/link/chunk issues, addressed separately below, and the DNS topic needs its scope decision. Those do not leave the map-nesting fix unverified. Validation of the writer's eventual source changes is part of the overall pre-check rerun.

**Owner:** Conversion engineer · **Timing:** Before the next AWS pre-check or conversion — configure both invocations to use the verified `-A` option

### Normalize image paths and copy the six existing assets

The [uncleaned `-A` build](logs/audit-verification.log) reports six missing-image errors because generated image hrefs point beside source stories/topics. All six assets exist under `aap-clouds/images/`: `AWS_tenant_isolation.png`, `automation_mesh.png`, `one-zone-subnet.png`, `many-zone-subnet.png`, `mesh_egress.png`, and `mesh_ingress.png`.

**Verified fix:** On an isolated copy of the existing AWS DITA, organize the topics into category directories, copy those six assets into the DITA root's `images/` directory, and apply standard image-path cleanup. All six rendered image references resolve, with zero `DOTX008E` errors. See the [engineer verification](audit-evidence/README.md#organized-output-verification) and [results](audit-evidence/verification.json).

**Writer prerequisite:** Complete the [image-alt macro source fix](#keep-image-alt-text-in-the-alt-attribute) above before final conversion. To isolate conversion behavior, this test removed only the invalid generated `width` value in its disposable DITA; it did not correct or verify the source macro. Overall readiness still requires the writer's correction and verification of its generated alt text and attributes.

**Owner:** Conversion engineer · **Timing:** Before conversion — identify and stage the six assets; during conversion — apply image-path cleanup, package the assets, and verify the writer's corrected macro in the final output

### Remove redundant chunking in the AsciiDoc category maps before conversion

The [organized and cleaned comparison build](logs/audit-verification.log) reports eight `Failed to move chunk` failures and ten each of `DOTX031E` and `DOTX026W`. The generated AsciiDoc category maps attach `chunk="to-content"` to includes of job maps. These become chunked DITA `<mapref>` elements even though each job map already chunks its parent topic. Standard path/reference cleanup alone does not fix this.

**Preferred fix before conversion:** In the following generated AsciiDoc category maps, remove only `,chunk="to-content"` from these job-map include directives. Preserve `leveloffset`, the job maps' own parent-topic chunk options, child topic references, `toc="no"`, and the approved JTBD hierarchy:

- In `aap-service-aws-plan.adoc`, correct includes of `red-hat-ansible-automation-platform-service-on-aws-pull-and-push-models.adoc`, `control-plane.adoc`, and `execution-plane.adoc`.
- In `aap-service-aws-get-started.adoc`, correct the include of `set-up-red-hat-ansible-automation-platform-service-on-aws.adoc`.
- In `aap-service-aws-administer.adoc`, correct includes of `account-management.adoc`, `support.adoc`, `red-hat-ansible-automation-platform-service-on-aws-subscription-lifecycle.adoc`, and `red-hat-ansible-automation-platform-best-practices.adoc`.
- In `aap-service-aws-configure.adoc`, correct the include of `red-hat-ansible-automation-platform-service-on-aws-private-link-connectivity.adoc`.
- In `aap-service-aws-optimize.adoc`, correct the include of `multiregion-activepassive-infrastructure.adoc`.

These are generated conversion-input maps, so their preparation belongs to the conversion engineer. Make map generation omit these redundant options, or apply the ten include corrections after generation and before invoking the converter. Do not change the writer's job flags or hierarchy to work around a generated-map defect. A lasting generation/preparation implementation has not been installed.

**Source correction verified:** Focused conversion of all five corrected category maps produces no chunked maprefs. Canonical XML comparison confirms their output exactly matches the accepted `-A` maps with only the ten mapref chunk attributes removed; the job source maps are byte-for-byte unchanged. See the [source-fix results](audit-evidence/verification.json) and [category-map conversion logs](logs/audit-verification.log).

The equivalent DITA correction was already verified in the [full-navigation XHTML build](audit-evidence/README.md#organized-output-verification): exit 0, no DITA diagnostic warnings/errors, all 99 directly selected topics reachable, every topic ID in the ten job chunks present in its rendered job page, and all 194 rendered local link/image references resolving to existing files and anchors ([details](audit-evidence/verification.json)). Retain removal of `chunk` from those ten DITA maprefs only as a fallback for already-converted output. The writer's source-loss findings and DNS scope decision remain unresolved by this test.

**Owner:** Conversion engineer · **Timing:** Before conversion — correct generated AsciiDoc map includes and prevent regeneration from reintroducing the options; during conversion — verify intended job chunks and final navigation

### Normalize the uppercase CIDR filename during standard conversion renaming

[`vale.log`](logs/vale.log) flags `aap-clouds/stories/assembly-saas-execution-plane.adoc:29` and generated `execution-plane.adoc:15` because the included filename contains uppercase `CIDR`. This is a generated-DITA naming requirement handled by the standard conversion rename step.

**Action:** Run the standard rename step after organization. It changes `con-saas-networking-with-overlapping-CIDR-blocks.dita` to `con-saas-networking-with-overlapping-cidr-blocks.dita` and updates DITA references. Keep the source `.adoc` filename, source includes, and JTBD/export paths unchanged for this finding; no writer action is required.

**Verified:** The retained [AWS rename test](logs/audit-verification.log) exercised the standard filename-normalization and reference-rewrite logic on disposable organized output. It updated the one map reference and left zero references with uppercase `CIDR`; the lowercase topic remained reachable in the full-navigation build ([results](audit-evidence/verification.json)). Verify the lowercase filename and references in the final output.

**Owner:** Conversion engineer · **Timing:** During conversion — run standard filename normalization after organization and verify updated references

The [evidence index](audit-evidence/README.md) explains the retained input snapshot, consolidated verification output, and removal of empty logs. The original empty `conditionals.log` supplied no result; use the retained AWS-specific scan and source review above.

+++
title = 'Nextflow caching and resuming'
weight = 8
date = 2026-08-18
+++

# Nextflow caching and resuming

Curated pointers to the canonical Nextflow and Seqera documentation on task caching, resume behavior, and the moving parts that decide when a re-run reuses previous work versus starts from scratch. Useful anytime you launch a pipeline on APGAP, whether through a notebook or through the Seqera Platform Launchpad.

## Nextflow engine

Primary reference: [Caching and resuming (Seqera Nextflow docs)](https://docs.seqera.io/nextflow/cache-and-resume)

| Section | Direct link | What it covers |
| --- | --- | --- |
| Task hash | [#task-hash](https://docs.seqera.io/nextflow/cache-and-resume#task-hash) | The exact hash inputs: session ID, task name, container, modules, Conda/Spack, inputs, script, `ext`, bundled `bin/` scripts, stub flag. Where the "safe to change vs. invalidates" split comes from. |
| Work directory | [#work-directory](https://docs.seqera.io/nextflow/cache-and-resume#work-directory) | Each task gets a unique dir keyed by its hash; Nextflow stages inputs and script in and writes outputs there. Both the storage that gets billed and the reason a new hash means a new dir. |
| Cache stores | [#cache-stores](https://docs.seqera.io/nextflow/cache-and-resume#cache-stores) | Local `.nextflow/cache` vs. cloud cache, and `NXF_CLOUDCACHE_PATH` for transient cloud VMs. |
| Troubleshooting → Modified inputs | [#modified-inputs](https://docs.seqera.io/nextflow/cache-and-resume#modified-inputs) | The invalidation list, plus the input-file hash being path + mtime + size. |
| Troubleshooting → Inconsistent file attributes | [#inconsistent-file-attributes](https://docs.seqera.io/nextflow/cache-and-resume#inconsistent-file-attributes) | NFS/timestamp cache busting and the `cache 'lenient'` fix. |
| Troubleshooting → Resume not enabled | [#resume-not-enabled](https://docs.seqera.io/nextflow/cache-and-resume#resume-not-enabled) | Why a plain re-run without `-resume` re-executes everything. |
| Tips → Resuming from a specific run | [#resuming-from-a-specific-run](https://docs.seqera.io/nextflow/cache-and-resume#resuming-from-a-specific-run) | `-resume` reuses the previous session by default; pass a session ID to resume an earlier run. |
| Tips → Comparing the hashes of two runs | [#comparing-the-hashes-of-two-runs](https://docs.seqera.io/nextflow/cache-and-resume#comparing-the-hashes-of-two-runs) | The `-dump-hashes json` diff recipe for finding exactly what moved a hash. |

## Seqera Platform and supporting pages

| Page | What it covers |
| --- | --- |
| [Platform: Nextflow cache and resume](https://docs.seqera.io/platform-cloud/launch/cache-resume) | The Resume vs. Relaunch distinction on the Platform. Relaunch runs from scratch with edited parameters and is only available for Platform-launched runs; Resume uses Nextflow's resume to reuse completed tasks and execute only failed and pending ones. |
| [Nextflow: `cache` directive](https://docs.seqera.io/nextflow/reference/process#cache) | `cache 'lenient'` and `cache false` semantics. |
| [Nextflow: `clean` command](https://docs.seqera.io/nextflow/reference/cli/clean) | Deleting work dirs and cache entries for specific runs. |
| [Seqera blog: Get started on Google Batch](https://seqera.io/blog/nextflow-with-gbatch/) | The plain statement behind the storage question: the workDir is where Nextflow stages input data and stores intermediate and final data. |
| [Nextflow: Google Cloud executor](https://nextflow.io/docs/latest/google.html) | `workDir` must be a bucket subdirectory; Fusion configuration. |

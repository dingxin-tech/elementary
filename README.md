## Community-maintained fork of the original [elementary](https://github.com/elementary-data/elementary) package with extended support for [dbt-maxcompute](https://github.com/aliyun/dbt-maxcompute) (dbt adapter of Alibaba Cloud MaxCompute).

<p align="center">
<img alt="Logo" src="https://raw.githubusercontent.com/elementary-data/elementary/master/static/github_banner.png"/ width="1000">
</p>

# [dbt native data observability](https://www.elementary-data.com/)

<p align="center">
<a href="https://join.slack.com/t/elementary-community/shared_invite/zt-uehfrq2f-zXeVTtXrjYRbdE_V6xq4Rg"><img src="https://img.shields.io/badge/join-Slack-ff69b4"/></a>
<a href="https://docs.elementary-data.com/quickstart"><img src="https://img.shields.io/badge/docs-quickstart-orange"/></a>
<img alt="License" src="https://img.shields.io/badge/license-Apache--2.0-ff69b4"/>
<img alt="Downloads" src="https://static.pepy.tech/personalized-badge/elementary-lineage?period=total&units=international_system&left_color=grey&right_color=orange"&left_text=Downloads"/>
</p>

## What is Elementary?

This dbt package is part of Elementary, the dbt-native data observability solution for data and analytics engineers.
Set up in minutes, gain immediate visibility, detect data issues, send actionable alerts, and understand impact and root cause.
Available as self-hosted or Cloud service with premium features.

#### Table of Contents

- [Quick start - dbt package](#quick-start---dbt-package)
- [Get more out of Elementary](#get-more-out-of-elementary-dbt-package)
- [Run results and dbt artifacts](#run-results-and-dbt-artifacts)
- [Data anomaly detection as dbt tests](#data-anomaly-detection-as-dbt-tests)
- [How Elementary works?](#how-elementary-works)
- [Community & Support](#community--support)
- [Contribution](#contributions)

## Quick start - dbt package

1. Add to your `packages.yml`:

```yml packages.yml
packages:
  - git: "https://github.com/dingxin-tech/elementary.git"
    revision: main
    ## Docs: https://docs.elementary-data.com
```

2. Run `dbt deps`

3. Add to your `dbt_project.yml`:

```yml
models:
  ## elementary models will be created in the schema '<your_schema>_elementary'
  ## for details, see docs: https://docs.elementary-data.com/
  elementary:
    +schema: "elementary"
```

4. Run `dbt run --select elementary`

Check out the [full documentation](https://docs.elementary-data.com/).

## Get more out of Elementary dbt package

Elementary has 3 offerings: This dbt package, Elementary Community (OSS) and Elementary (cloud service).

- **dbt package**
  - For basic data monitoring and dbt artifacts collection, Elementary offers a dbt package. The package adds logging, artifacts uploading, and Elementary tests (anomaly detection and schema) to your project.
- **Elementary Community**
  - An open-source CLI tool you can deploy and orchestrate to send alerts and self-host the Elementary report. Best for data and analytics engineers that require basic observability capabilities or for evaluating features without vendor approval. Our community can provide great support on [Slack](https://www.elementary-data.com/community) if needed.
- **Elementary Cloud**
  - Ideal for teams monitoring mission-critical data pipelines, requiring guaranteed uptime and reliability, short-time-to-value, advanced features, collaboration, and professional support. The solution is secure by design, and requires no access to your data from cloud. To learn more, [book a demo](https://cal.com/maayansa/elementary-intro-github-package) or [start a trial](https://www.elementary-data.com/signup).

## Run Results and dbt artifacts

The package automatically uploads dbt artifacts and run results to tables in your data warehouse:

Run results tables:

- dbt_run_results
- model_run_results
- snapshot_run_results
- dbt_invocations
- elementary_test_results (all dbt test results)

Metadata tables:

- dbt_models
- dbt_tests
- dbt_sources
- dbt_exposures
- dbt_metrics
- dbt_snapshots

Here you can find [additional details about the tables](https://docs.elementary-data.com/guides/modules-overview/dbt-package).

## Data anomaly detection as dbt tests

Elementary dbt tests collect metrics and metadata over time, such as freshness, volume, schema changes, distribution, cardinality, etc.
Executed as any other dbt tests, the Elementary tests alert on anomalies and outliers.

**Elementary tests are configured and executed like native tests in your project!**

Example of Elementary test config in `properties.yml`:

```yml
models:
  - name: your_model_name
    config:
      elementary:
        timestamp_column: updated_at
    tests:
      - elementary.table_anomalies
      - elementary.all_columns_anomalies
```

Read about the available [Elementary tests and configuration](https://docs.elementary-data.com/data-tests/introduction).

## How Elementary works?

Elementary dbt package creates tables of metadata and test results in your data warehouse, as part of your dbt runs.

The cloud service or the CLI tool read the data from these tables, send alerts and present the results in the UI.

<kbd align="center">
        <a href="https://storage.googleapis.com/elementary_static/elementary_demo.html"><img align="center" style="max-width:300px;" src="https://raw.githubusercontent.com/elementary-data/elementary/master/static/report_ui.gif"> </a>
</kbd>

## Community & Support

- [Slack](https://join.slack.com/t/elementary-community/shared_invite/zt-uehfrq2f-zXeVTtXrjYRbdE_V6xq4Rg) (Talk to us, support, etc.)
- [GitHub issues](https://github.com/elementary-data/elementary/issues) (Bug reports, feature requests)

## Contributions

Thank you :orange_heart: Whether it’s a bug fix, new feature, or additional documentation - we greatly appreciate contributions!

Check out the [contributions guide](https://docs.elementary-data.com/general/contributions) and [open issues](https://github.com/elementary-data/elementary/issues) in the main repo.

## Dependency pinning

`packages.yml` pins **dbt-utils** to tag `1.3.0-mc.1`:

```yaml
- git: "https://github.com/dingxin-tech/dbt-utils.git"
  revision: 1.3.0-mc.1
```

`package-lock.yml` in this directory records what that resolved to:

    dbt-utils -> 3463ce1e4bf86fe6e11ae7bf05ef04ffc04a3a1a

Before this change the dependency floated on a branch (`revision: main`), so two clean `dbt deps` runs on different
days could install different dbt-utils source, and nothing in the project said which one you got.
dbt-utils `1.3.0-mc.1` is the revision the MaxCompute compatibility run of 2026-09-25 built green (9/9 representative models).

Check it yourself, in a clean checkout:

```bash
python dev-tools/check_package_pins.py      # R1-R4: immutable refs, lock in sync, tags unmoved
rm -rf dbt_packages package-lock.yml && dbt deps && git diff --exit-code package-lock.yml
```

### Installing this package reproducibly

```yaml
# your_project/packages.yml
packages:
  - git: "https://github.com/dingxin-tech/elementary.git"
    revision: 0.16.4-mc.1
```

Pin tag in your own project, then commit the `package-lock.yml` that `dbt deps` writes there: it holds the full commit sha, so a rebuild years from now installs this same code.

```bash
rm -rf dbt_packages package-lock.yml && dbt deps   # first install: resolves, installs, writes the lock
dbt deps                                           # later runs: install exactly what the committed lock records
dbt deps --lock                                    # regenerate the lock file only - it installs nothing
```

`--lock` is not an install command. On dbt-core 1.11 `dbt deps --help` describes it as
"Generate the package-lock.yml file without install the packages." - it writes
`package-lock.yml` and stops there. Reproducing an install is plain `dbt deps`: with a
committed lock and an unchanged `packages.yml`, that is what reads the lock as its source
of truth, and it is what a rebuild months later runs.

### Upgrading a dependency

1. Read the current MaxCompute support level first: the _Compatible dbt Packages_ table in the
   [dbt-maxcompute README](https://github.com/aliyun/dbt-maxcompute).
2. Change `revision:` (or `version:`) to a tag or a full 40-char commit sha. Never a branch name -
   `.github/workflows/deps-lock-check.yml` fails on that.
3. Regenerate from scratch: `rm -rf dbt_packages package-lock.yml && dbt deps`, then
   `python dev-tools/check_package_pins.py` and a real `dbt build` against a three-tier MaxCompute project.
4. Commit `packages.yml` and `package-lock.yml` together so a revert is atomic.

---
url: https://turborepo.dev/docs/reference/options-overview
title: "Options overview"
description: "Flags, configurations, and System Environment Variables for Turborepo"
access_date: 2026-08-03T19:10:40.966Z
current_date: 2026-08-03T19:10:40.966Z
---

# Options overview



There are three ways to manage the behavior of a `turbo` invocation:

* [Configuration in `turbo.json`](configuration.md)
* [System Environment Variables](system-environment-variables.md)
* [Flags passed to the CLI invocation](run.md)

The three strategies listed above are in order of precedence. Where a flag value is provided, for the same System Environment Variable or `turbo.json` configuration, the value for the flag will be used. Because of this, we recommend using:

* `turbo.json` configuration for defaults
* System Environment Variables for per-environment overrides
* Flags for per-invocation overrides

## Options table

### Caching

<div className="options-cheat-sheet-table">
  | Behavior                    | Flags                                                             | Environment Variables                                                                                                 | turbo.json                                                                 |
  | --------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
  | Force tasks to run          | [`--force`](run.md#--force)                          | [`TURBO_FORCE`](system-environment-variables.md#turbo_force)                                             | [`cache`](configuration.md#cache)                             |
  | Remote Cache timeout        | [`--remote-cache-timeout`](configuration.md#timeout) | [`TURBO_REMOTE_CACHE_TIMEOUT`](system-environment-variables.md#turbo_remote_cache_timeout)               | [`remoteCache.timeout`](configuration.md#timeout)             |
  | Remote Cache upload timeout | -                                                                 | [`TURBO_REMOTE_CACHE_UPLOAD_TIMEOUT`](system-environment-variables.md#turbo_remote_cache_upload_timeout) | [`remoteCache.uploadTimeout`](configuration.md#uploadtimeout) |
  | Cache signature key         | -                                                                 | [`TURBO_REMOTE_CACHE_SIGNATURE_KEY`](system-environment-variables.md#turbo_remote_cache_signature_key)   | [`signature`](configuration.md#signature)                     |
  | Preflight request           | [`--preflight`](run.md#--preflight)                  | [`TURBO_PREFLIGHT`](system-environment-variables.md#turbo_preflight)                                     | [`remoteCache.preflight`](configuration.md#preflight)         |
  | Remote Cache base URL       | -                                                                 | [`TURBO_API`](system-environment-variables.md#turbo_api)                                                 | [`remoteCache.apiUrl`](configuration.md#remote-caching)       |
  | Cache sources               | [`--cache`](run.md#--cache-options)                  | [`TURBO_CACHE`](system-environment-variables.md#turbo_cache)                                             | -                                                                          |
  | Local cache directory       | [`--cache-dir`](run.md#--cache-dir-path)             | [`TURBO_CACHE_DIR`](system-environment-variables.md#turbo_cache_dir)                                     | [`cacheDir`](configuration.md#cachedir)                       |
  | Local cache max age         | -                                                                 | [`TURBO_CACHE_MAX_AGE`](system-environment-variables.md#turbo_cache_max_age)                             | [`cacheMaxAge`](configuration.md#cachemaxage)                 |
  | Local cache max size        | -                                                                 | [`TURBO_CACHE_MAX_SIZE`](system-environment-variables.md#turbo_cache_max_size)                           | [`cacheMaxSize`](configuration.md#cachemaxsize)               |
</div>

### Messages

<div className="options-cheat-sheet-table">
  | Behavior                       | Flags | Environment Variables                                                                                               | turbo.json                                                                                |
  | ------------------------------ | ----- | ------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
  | Disable version print          | -     | [`TURBO_PRINT_VERSION_DISABLED`](system-environment-variables.md#turbo_print_version_disabled)         | -                                                                                         |
  | Disable telemetry message      | -     | [`TURBO_TELEMETRY_MESSAGE_DISABLED`](system-environment-variables.md#turbo_telemetry_message_disabled) | -                                                                                         |
  | Disable global `turbo` warning | -     | [`TURBO_GLOBAL_WARNING_DISABLED`](system-environment-variables.md#turbo_global_warning_disabled)       | -                                                                                         |
  | No update notifier             | -     | [`TURBO_NO_UPDATE_NOTIFIER`](system-environment-variables.md#turbo_no_update_notifier)                 | [`noUpdateNotifier`](configuration.md#noupdatenotifier) |
</div>

### Task running and logs

<div className="options-cheat-sheet-table">
  | Behavior                          | Flags                                                                                                            | Environment Variables                                                                                                                             | turbo.json                                                                                                     |
  | --------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
  | Terminal UI                       | [`--ui`](run.md#--ui)                                                                               | [`TURBO_UI`](system-environment-variables.md#turbo_ui)                                                                               | [`ui`](configuration.md#ui)                                                                       |
  | Run affected tasks                | [`--affected`](run.md#--affected)                                                                   | -                                                                                                                                                 | -                                                                                                              |
  | Disable package manager check     | [`--dangerously-disable-package-manager-check`](run.md#--dangerously-disable-package-manager-check) | [`TURBO_DANGEROUSLY_DISABLE_PACKAGE_MANAGER_CHECK`](system-environment-variables.md#turbo_dangerously_disable_package_manager_check) | [`dangerouslyDisablePackageManagerCheck`](configuration.md#dangerouslydisablepackagemanagercheck) |
  | Affected base ref                 | -                                                                                                                | [`TURBO_SCM_BASE`](system-environment-variables.md#turbo_scm_base)                                                                   | -                                                                                                              |
  | Affected head ref                 | -                                                                                                                | [`TURBO_SCM_HEAD`](system-environment-variables.md#turbo_scm_head)                                                                   | -                                                                                                              |
  | Only run directly specified tasks | [`--only`](run.md#--only)                                                                           | -                                                                                                                                                 | -                                                                                                              |
  | Task concurrency                  | [`--concurrency`](run.md#--concurrency-number--percentage)                                          | [`TURBO_CONCURRENCY`](system-environment-variables.md#turbo_concurrency)                                                             | -                                                                                                              |
  | Task log order                    | [`--log-order`](run.md#--log-order-option)                                                          | [`TURBO_LOG_ORDER`](system-environment-variables.md#turbo_log_order)                                                                 | -                                                                                                              |
  | Current working directory         | [`--cwd`](run.md#--cwd-path)                                                                        | -                                                                                                                                                 | -                                                                                                              |
  | Streamed logs prefix              | [`--log-prefix`](run.md#--log-prefix-option)                                                        | -                                                                                                                                                 | -                                                                                                              |
  | Task logs output level            | [`--output-logs-option`](run.md#--output-logs-option)                                               | -                                                                                                                                                 | [`outputLogs`](configuration.md#outputlogs)                                                       |
  | Global inputs                     | [`--global-deps`](run.md#--global-deps-file-glob)                                                   | -                                                                                                                                                 | [`globalDependencies`](configuration.md#globaldependencies)                                       |
  | Terminal colors                   | [`--color`](../reference.md#--color)                                                                             | [FORCE\_COLOR](system-environment-variables.md#force_color)                                                                          | -                                                                                                              |
</div>

### Environment variables

<div className="options-cheat-sheet-table">
  | Behavior                      | Flags                                                                | Environment Variables                                                                             | turbo.json                                         |
  | ----------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
  | Environment variable mode     | [`--env-mode`](run.md#--env-mode-option)                | -                                                                                                 | [`envMode`](configuration.md#envmode) |
  | Vendor environment variables  | -                                                                    | [`TURBO_CI_VENDOR_ENV_KEY`](system-environment-variables.md#turbo_ci_vendor_env_key) | -                                                  |
  | Framework variable exceptions | [`--framework-inference`](run.md#--framework-inference) | -                                                                                                 | -                                                  |
</div>

### Debugging outputs

<div className="options-cheat-sheet-table">
  | Behavior            | Flags                                              | Environment Variables                                                                 | turbo.json |
  | ------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------- | ---------- |
  | Run Summaries       | [`--summarize`](run.md#--summarize)   | [`TURBO_RUN_SUMMARY`](system-environment-variables.md#turbo_run_summary) | -          |
  | Graph visualization | [`--graph`](run.md#--graph-file-name) | -                                                                                     | -          |
  | Dry run             | [`--dry`](run.md#--dry----dry-run)    | -                                                                                     | -          |
</div>

### Authentication

<div className="options-cheat-sheet-table">
  | Behavior                                | Flags                                    | Environment Variables                                                       | turbo.json                                                       |
  | --------------------------------------- | ---------------------------------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------------- |
  | Login URL                               | -                                        | [`TURBO_LOGIN`](system-environment-variables.md#turbo_login)   | [`remoteCache.loginUrl`](configuration.md#loginurl) |
  | Team name (for multi-team Remote Cache) | [`--team`](run.md#--team)   | [`TURBO_TEAM`](system-environment-variables.md#turbo_team)     | -                                                                |
  | Team ID (for multi-team Remote Cache)   | -                                        | [`TURBO_TEAMID`](system-environment-variables.md#turbo_teamid) | -                                                                |
  | Authentication token                    | [`--token`](run.md#--token) | [`TURBO_TOKEN`](system-environment-variables.md#turbo_token)   | -                                                                |
</div>

### Other

<div className="options-cheat-sheet-table">
  | Behavior               | Flags                                                                      | Environment Variables                                                                                       | turbo.json                                       |
  | ---------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
  | Binary path            | -                                                                          | [`TURBO_BINARY_PATH`](system-environment-variables.md#turbo_binary_path)                       | -                                                |
  | Download local `turbo` | -                                                                          | [`TURBO_DOWNLOAD_LOCAL_ENABLED`](system-environment-variables.md#turbo_download_local_enabled) | -                                                |
  | Daemon (deprecated)    | [`--daemon` / `--no-daemon`](run.md#--daemon-and---no-daemon) | -                                                                                                           | [`daemon`](configuration.md#daemon) |
</div>


---

For a semantic overview of all documentation, see [/sitemap.md](https://turborepo.dev/sitemap.md)

For an index of all available documentation, see [/llms.txt](https://turborepo.dev/llms.txt)

For agent-facing discovery, including API and MCP surfaces, see [/agents.md](https://turborepo.dev/agents.md)

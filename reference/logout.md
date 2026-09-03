---
url: https://turborepo.dev/docs/reference/logout
title: "logout"
description: "Reference for the `turbo logout` command that signs out of your Remote Cache provider."
access_date: 2026-09-03T21:18:59.663Z
current_date: 2026-09-03T21:18:59.663Z
---

[API reference](../reference.md)

API reference for the \`turbo logout\` command

Log out of the account associated with your Remote Cache provider.

```
turbo logout
```

## Flags

### \--invalidate \[<BOOL>\]

Invalidate the token on the server. Defaults to `true`.

Pass `--invalidate=false` to remove the local token without revoking it on the server.

```
turbo logout --invalidate=false
```

[Previouslogin](login.md) [Nextlink](link.md)

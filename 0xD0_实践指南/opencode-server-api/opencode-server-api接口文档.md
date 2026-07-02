# opencode 本地服务 API 接口文档（请求/响应格式）

> 来源: opencode v1.17.13 内置 HTTP 服务 OpenAPI 3.1.0 规范（`GET /doc`）
> 采集方式: `opencode serve --port <port>` 启动本地服务后抓取
> 服务默认地址: `http://127.0.0.1:<port>`，正文中以 `{baseUrl}` 表示
> 说明: 每个接口按「请求 / 响应」两段给出；JSON 中 `{...}` 为字段占位说明（含类型、是否必填、可选值等），非真实数据

- 接口总数: 188
- 分组(tag): 32

机器可读完整规范见同目录 `opencode-openapi.json`。

---

## 目录

- [commands (1)](#commands)
- [config (3)](#config)
- [control (3)](#control)
- [controlPlane (1)](#controlplane)
- [event (1)](#event)
- [events (1)](#events)
- [experimental (13)](#experimental)
- [file (6)](#file)
- [filesystem (3)](#filesystem)
- [global (6)](#global)
- [instance (12)](#instance)
- [integrations (7)](#integrations)
- [mcp (8)](#mcp)
- [messages (1)](#messages)
- [models (1)](#models)
- [opencode HttpApi (5)](#opencode-httpapi)
- [permission (2)](#permission)
- [permissions (7)](#permissions)
- [project (5)](#project)
- [projectCopy (4)](#projectcopy)
- [provider (4)](#provider)
- [providers (2)](#providers)
- [pty (15)](#pty)
- [question (3)](#question)
- [reference (1)](#reference)
- [session (27)](#session)
- [session questions (4)](#session-questions)
- [sessions (17)](#sessions)
- [skills (1)](#skills)
- [sync (4)](#sync)
- [tui (13)](#tui)
- [workspace (7)](#workspace)

---

## commands

### List commands

**operationId**: `v2.command.list`

> Retrieve currently registered commands.

#### 请求

```
GET {baseUrl}/api/command?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "name": "{必填，string}",
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": {
        "id": "{必填，string}",
        "providerID": "{必填，string}",
        "variant": "{选填，string}"
      },
      "subtask": "{选填，boolean}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## config

### Get configuration

**operationId**: `config.get`

> Retrieve the current OpenCode configuration settings and preferences.

#### 请求

```
GET {baseUrl}/config?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Get config info

```
Content-Type: application/json
```

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Update configuration

**operationId**: `config.update`

> Update OpenCode configuration settings and preferences.

#### 请求

```
PATCH {baseUrl}/config?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

#### 响应

**HTTP 200** Successfully updated config

```
Content-Type: application/json
```

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### List config providers

**operationId**: `config.providers`

> Get a list of all configured AI providers and their default models.

#### 请求

```
GET {baseUrl}/config/providers?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of providers

```
Content-Type: application/json
```

```json
{
  "providers": [
    {
      "id": "{必填，string}",
      "name": "{必填，string}",
      "source": "{必填，可选值: 'env', 'config', 'custom', 'api'}",
      "env": [
        "{string}"
      ],
      "key": "{选填，string}",
      "options": {},
      "models": {
        "{键}": "{Model 对象}"
      }
    }
  ],
  "default": {
    "{键}": "{string}"
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## control

### Remove auth credentials

**operationId**: `auth.remove`

> Remove authentication credentials

#### 请求

```
DELETE {baseUrl}/auth/{providerID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `providerID` | path | 是 | string |  |

#### 响应

**HTTP 200** Successfully removed authentication credentials

```
Content-Type: application/json
```

```json
"{Successfully removed authentication credentials，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Set auth credentials

**operationId**: `auth.set`

> Set authentication credentials

#### 请求

```
PUT {baseUrl}/auth/{providerID}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `providerID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "type": "{必填，可选值: 'oauth'}",
  "refresh": "{必填，string}",
  "access": "{必填，string}",
  "expires": "{必填，integer}",
  "accountId": "{选填，string}",
  "enterpriseUrl": "{选填，string}"
}
```

#### 响应

**HTTP 200** Successfully set authentication credentials

```
Content-Type: application/json
```

```json
"{Successfully set authentication credentials，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Write log

**operationId**: `app.log`

> Write a log entry to the server logs with specified level and metadata.

#### 请求

```
POST {baseUrl}/log?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "service": "{必填，Service name for the log entry，string}",
  "level": "{必填，Log level，可选值: 'debug', 'info', 'error', 'warn'}",
  "message": "{必填，Log message，string}",
  "extra": {}
}
```

#### 响应

**HTTP 200** Log entry written successfully

```
Content-Type: application/json
```

```json
"{Log entry written successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

## controlPlane

### Move session

**operationId**: `experimental.controlPlane.moveSession`

> Move a session to another project directory, optionally transferring local changes.

#### 请求

```
POST {baseUrl}/experimental/control-plane/move-session
Content-Type: application/json
```

**请求体** (`application/json`):

```json
{
  "sessionID": "{必填，string}",
  "destination": {
    "directory": "{必填，string}"
  },
  "moveChanges": "{选填，boolean}"
}
```

#### 响应

**HTTP 204** Session moved

（无响应体）

**HTTP 400** MoveSessionError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'MoveSessionError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

## event

### Subscribe to events

**operationId**: `event.subscribe`

> Get events

#### 请求

```
GET {baseUrl}/event?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Event stream

```
Content-Type: text/event-stream
```

```json
{
  "id": "{必填，string}",
  "type": "{必填，可选值: 'models-dev.refreshed'}",
  "properties": {}
}
```

---

## events

### Subscribe to events

**operationId**: `v2.event.subscribe`

> Subscribe to native event payloads for the server.

#### 请求

```
GET {baseUrl}/api/event
```

#### 响应

**HTTP 200** Event stream

```
Content-Type: text/event-stream
```

```json
{
  "id": "{必填，string}",
  "metadata": {},
  "type": "{必填，可选值: 'models-dev.refreshed'}",
  "durable": {
    "aggregateID": "{必填，string}",
    "seq": "{必填，integer}",
    "version": "{必填，integer}"
  },
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}"
  },
  "data": {}
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## experimental

### Get experimental capabilities

**operationId**: `experimental.capabilities.get`

> Get experimental features enabled on the OpenCode server.

#### 请求

```
GET {baseUrl}/experimental/capabilities?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Experimental capabilities

```
Content-Type: application/json
```

```json
{
  "backgroundSubagents": "{必填，boolean}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get active Console provider metadata

**operationId**: `experimental.console.get`

> Get the active Console org name and the set of provider IDs managed by that Console org.

#### 请求

```
GET {baseUrl}/experimental/console?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Active Console provider metadata

```
Content-Type: application/json
```

```json
{
  "consoleManagedProviders": [
    "{string}"
  ],
  "activeOrgName": "{选填，string}",
  "switchableOrgCount": "{必填，integer}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 500** InternalServerError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InternalServerError'}"
}
```

---

### List switchable Console orgs

**operationId**: `experimental.console.listOrgs`

> Get the available Console orgs across logged-in accounts, including the current active org.

#### 请求

```
GET {baseUrl}/experimental/console/orgs?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Switchable Console orgs

```
Content-Type: application/json
```

```json
{
  "orgs": [
    {
      "accountID": "{必填，string}",
      "accountEmail": "{必填，string}",
      "accountUrl": "{必填，string}",
      "orgID": "{必填，string}",
      "orgName": "{必填，string}",
      "active": "{必填，boolean}"
    }
  ]
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 500** InternalServerError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InternalServerError'}"
}
```

---

### Switch active Console org

**operationId**: `experimental.console.switchOrg`

> Persist a new active Console account/org selection for the current local OpenCode state.

#### 请求

```
POST {baseUrl}/experimental/console/switch?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "accountID": "{必填，string}",
  "orgID": "{必填，string}"
}
```

#### 响应

**HTTP 200** Switch success

```
Content-Type: application/json
```

```json
"{Switch success，boolean}"
```

---

### Get MCP resources

**operationId**: `experimental.resource.list`

> Get all available MCP resources from connected servers. Optionally filter by name.

#### 请求

```
GET {baseUrl}/experimental/resource?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** MCP resources

```
Content-Type: application/json
```

```json
{
  "{键}": {
    "name": "{必填，string}",
    "uri": "{必填，string}",
    "description": "{选填，string}",
    "mimeType": "{选填，string}",
    "client": "{必填，string}"
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### List sessions

**operationId**: `experimental.session.list`

> Get a list of all OpenCode sessions across projects, sorted by most recently updated. Archived sessions are excluded by default.

#### 请求

```
GET {baseUrl}/experimental/session?directory={directory}&workspace={workspace}&roots={roots}&start={start}&cursor={cursor}&search={search}&limit={limit}&archived={archived}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `roots` | query | 否 | boolean \| "true"\|"false" |  |
| `start` | query | 否 | number |  |
| `cursor` | query | 否 | number |  |
| `search` | query | 否 | string |  |
| `limit` | query | 否 | number |  |
| `archived` | query | 否 | boolean \| "true"\|"false" |  |

#### 响应

**HTTP 200** List of sessions

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "slug": "{必填，string}",
    "projectID": "{必填，string}",
    "workspaceID": "{选填，string}",
    "directory": "{必填，string}",
    "path": "{选填，string}",
    "parentID": "{选填，string}",
    "summary": {
      "additions": "{必填，number}",
      "deletions": "{必填，number}",
      "files": "{必填，number}",
      "diffs": [
        "{SnapshotFileDiff 对象}"
      ]
    },
    "cost": "{选填，number}",
    "tokens": {
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "share": {
      "url": "{必填，string}"
    },
    "title": "{必填，string}",
    "agent": "{选填，string}",
    "model": {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "version": "{必填，string}",
    "metadata": {},
    "time": {
      "created": "{必填，integer}",
      "updated": "{必填，integer}",
      "compacting": "{选填，integer}",
      "archived": "{选填，number}"
    },
    "permission": [
      "{PermissionRule 对象}"
    ],
    "revert": {
      "messageID": "{必填，string}",
      "partID": "{选填，string}",
      "snapshot": "{选填，string}",
      "diff": "{选填，string}"
    },
    "project": {
      "id": "{必填，string}",
      "name": "{选填，string}",
      "worktree": "{必填，string}"
    }
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Background subagents

**operationId**: `experimental.session.background`

> Detach any synchronous subagents currently blocking the session and continue them in the background.

#### 请求

```
POST {baseUrl}/experimental/session/{sessionID}/background?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Backgrounded subagents

```
Content-Type: application/json
```

```json
"{Backgrounded subagents，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### List tools

**operationId**: `tool.list`

> Get a list of available tools with their JSON schema parameters for a specific provider and model combination.

#### 请求

```
GET {baseUrl}/experimental/tool?directory={directory}&workspace={workspace}&provider={provider}&model={model}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `provider` | query | 是 | string |  |
| `model` | query | 是 | string |  |

#### 响应

**HTTP 200** Tools

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "description": "{必填，string}",
    "parameters": "{必填，object}"
  }
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### List tool IDs

**operationId**: `tool.ids`

> Get a list of all available tool IDs, including both built-in tools and dynamically registered tools.

#### 请求

```
GET {baseUrl}/experimental/tool/ids?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Tool IDs

```
Content-Type: application/json
```

```json
[
  "{string}"
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Remove worktree

**operationId**: `worktree.remove`

> Remove a git worktree and delete its branch.

#### 请求

```
DELETE {baseUrl}/experimental/worktree?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "directory": "{必填，string}"
}
```

#### 响应

**HTTP 200** Worktree removed

```
Content-Type: application/json
```

```json
"{Worktree removed，boolean}"
```

**HTTP 400** WorktreeError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorktreeNotGitError', 'WorktreeNameGenerationFailedError', 'WorktreeCreateFailedError', 'WorktreeStartCommandFailedError', 'WorktreeRemoveFailedError', 'WorktreeResetFailedError', 'WorktreeListFailedError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### List worktrees

**operationId**: `worktree.list`

> List all sandbox worktrees for the current project.

#### 请求

```
GET {baseUrl}/experimental/worktree?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of worktree directories

```
Content-Type: application/json
```

```json
[
  "{string}"
]
```

**HTTP 400** WorktreeError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorktreeNotGitError', 'WorktreeNameGenerationFailedError', 'WorktreeCreateFailedError', 'WorktreeStartCommandFailedError', 'WorktreeRemoveFailedError', 'WorktreeResetFailedError', 'WorktreeListFailedError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Create worktree

**operationId**: `worktree.create`

> Create a new git worktree for the current project and run any configured startup scripts.

#### 请求

```
POST {baseUrl}/experimental/worktree?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "name": "{选填，string}",
  "startCommand": "{选填，Additional startup script to run after the project's start command，string}"
}
```

#### 响应

**HTTP 200** Worktree created

```
Content-Type: application/json
```

```json
{
  "name": "{必填，string}",
  "branch": "{选填，string}",
  "directory": "{必填，string}"
}
```

**HTTP 400** WorktreeError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorktreeNotGitError', 'WorktreeNameGenerationFailedError', 'WorktreeCreateFailedError', 'WorktreeStartCommandFailedError', 'WorktreeRemoveFailedError', 'WorktreeResetFailedError', 'WorktreeListFailedError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Reset worktree

**operationId**: `worktree.reset`

> Reset a worktree branch to the primary default branch.

#### 请求

```
POST {baseUrl}/experimental/worktree/reset?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "directory": "{必填，string}"
}
```

#### 响应

**HTTP 200** Worktree reset

```
Content-Type: application/json
```

```json
"{Worktree reset，boolean}"
```

**HTTP 400** WorktreeError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorktreeNotGitError', 'WorktreeNameGenerationFailedError', 'WorktreeCreateFailedError', 'WorktreeStartCommandFailedError', 'WorktreeRemoveFailedError', 'WorktreeResetFailedError', 'WorktreeListFailedError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

## file

### List files

**operationId**: `file.list`

> List files and directories in a specified path.

#### 请求

```
GET {baseUrl}/file?directory={directory}&workspace={workspace}&path={path}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `path` | query | 是 | string |  |

#### 响应

**HTTP 200** Files and directories

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "path": "{必填，string}",
    "absolute": "{必填，string}",
    "type": "{必填，可选值: 'file', 'directory'}",
    "ignored": "{必填，boolean}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Read file

**operationId**: `file.read`

> Read the content of a specified file.

#### 请求

```
GET {baseUrl}/file/content?directory={directory}&workspace={workspace}&path={path}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `path` | query | 是 | string |  |

#### 响应

**HTTP 200** File content

```
Content-Type: application/json
```

```json
{
  "type": "{必填，可选值: 'text', 'binary'}",
  "content": "{必填，string}",
  "diff": "{选填，string}",
  "patch": {
    "oldFileName": "{必填，string}",
    "newFileName": "{必填，string}",
    "oldHeader": "{选填，string}",
    "newHeader": "{选填，string}",
    "hunks": [
      {
        "oldStart": "{必填，integer}",
        "oldLines": "{必填，integer}",
        "newStart": "{必填，integer}",
        "newLines": "{必填，integer}",
        "lines": [
          "{string}"
        ]
      }
    ],
    "index": "{选填，string}"
  },
  "encoding": "{选填，可选值: 'base64'}",
  "mimeType": "{选填，string}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get file status

**operationId**: `file.status`

> Get the git status of all files in the project.

#### 请求

```
GET {baseUrl}/file/status?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** File status

```
Content-Type: application/json
```

```json
[
  {
    "path": "{必填，string}",
    "added": "{必填，integer}",
    "removed": "{必填，integer}",
    "status": "{必填，可选值: 'added', 'deleted', 'modified'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Find text

**operationId**: `find.text`

> Search for text patterns across files in the project using ripgrep.

#### 请求

```
GET {baseUrl}/find?directory={directory}&workspace={workspace}&pattern={pattern}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `pattern` | query | 是 | string |  |

#### 响应

**HTTP 200** Matches

```
Content-Type: application/json
```

```json
[
  {
    "path": {
      "text": "{必填，string}"
    },
    "lines": {
      "text": "{必填，string}"
    },
    "line_number": "{必填，integer}",
    "absolute_offset": "{必填，integer}",
    "submatches": [
      {
        "match": {
          "text": "{必填，string}"
        },
        "start": "{必填，integer}",
        "end": "{必填，integer}"
      }
    ]
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Find files

**operationId**: `find.files`

> Search for files or directories by name or pattern in the project directory.

#### 请求

```
GET {baseUrl}/find/file?directory={directory}&workspace={workspace}&query={query}&dirs={dirs}&type={type}&limit={limit}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `query` | query | 是 | string |  |
| `dirs` | query | 否 | "true"\|"false" |  |
| `type` | query | 否 | "file"\|"directory" |  |
| `limit` | query | 否 | integer |  |

#### 响应

**HTTP 200** File paths

```
Content-Type: application/json
```

```json
[
  "{string}"
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Find symbols

**operationId**: `find.symbols`

> Search for workspace symbols like functions, classes, and variables using LSP.

#### 请求

```
GET {baseUrl}/find/symbol?directory={directory}&workspace={workspace}&query={query}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `query` | query | 是 | string |  |

#### 响应

**HTTP 200** Symbols

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "kind": "{必填，integer}",
    "location": {
      "uri": "{必填，string}",
      "range": {
        "start": {
          "line": "{必填，integer}",
          "character": "{必填，integer}"
        },
        "end": {
          "line": "{必填，integer}",
          "character": "{必填，integer}"
        }
      }
    }
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## filesystem

### Find files

**operationId**: `v2.fs.find`

> Find recursively ranked filesystem entries relative to the requested location.

#### 请求

```
GET {baseUrl}/api/fs/find?location={location}&query={query}&type={type}&limit={limit}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |
| `query` | query | 是 | string |  |
| `type` | query | 否 | "file"\|"directory" |  |
| `limit` | query | 否 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "path": "{必填，string}",
      "type": "{必填，可选值: 'file', 'directory'}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### List directory

**operationId**: `v2.fs.list`

> List direct children of one directory relative to the requested location.

#### 请求

```
GET {baseUrl}/api/fs/list?location={location}&path={path}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |
| `path` | query | 否 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "path": "{必填，string}",
      "type": "{必填，可选值: 'file', 'directory'}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Read file

**operationId**: `v2.fs.read`

> Serve one file relative to the requested location.

#### 请求

```
GET {baseUrl}/api/fs/read/*?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/octet-stream
```

```json
"{string，格式:binary}"
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## global

### Get global configuration

**operationId**: `global.config.get`

> Retrieve the current global OpenCode configuration settings and preferences.

#### 请求

```
GET {baseUrl}/global/config
```

#### 响应

**HTTP 200** Get global config info

```
Content-Type: application/json
```

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Update global configuration

**operationId**: `global.config.update`

> Update global OpenCode configuration settings and preferences.

#### 请求

```
PATCH {baseUrl}/global/config
Content-Type: application/json
```

**请求体** (`application/json`):

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

#### 响应

**HTTP 200** Successfully updated global config

```
Content-Type: application/json
```

```json
{
  "$schema": "{选填，string}",
  "shell": "{选填，string}",
  "logLevel": "{选填，LogLevel 对象}",
  "server": {
    "port": "{选填，integer}",
    "hostname": "{选填，string}",
    "mdns": "{选填，boolean}",
    "mdnsDomain": "{选填，string}",
    "cors": [
      "{string}"
    ]
  },
  "command": {
    "{键}": {
      "template": "{必填，string}",
      "description": "{选填，string}",
      "agent": "{选填，string}",
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "subtask": "{选填，boolean}"
    }
  },
  "skills": {
    "paths": [
      "{string}"
    ],
    "urls": [
      "{string}"
    ]
  },
  "references": {
    "{键}": "{string}"
  },
  "reference": {
    "{键}": "{string}"
  },
  "watcher": {
    "ignore": [
      "{string}"
    ]
  },
  "snapshot": "{选填，boolean}",
  "plugin": [
    "{string}"
  ],
  "share": "{选填，可选值: 'manual', 'auto', 'disabled'}",
  "autoshare": "{选填，boolean}",
  "autoupdate": "{选填，Automatically update to the latest version. Set to true to auto-update, false to disable, or 'notify' to show update notifications，boolean | \"notify\"}",
  "disabled_providers": [
    "{string}"
  ],
  "enabled_providers": [
    "{string}"
  ],
  "model": "{选填，string}",
  "small_model": "{选填，string}",
  "default_agent": "{选填，string}",
  "username": "{选填，string}",
  "mode": {
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "agent": {
    "plan": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "build": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "general": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "explore": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "title": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "summary": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    },
    "compaction": {
      "model": "{选填，string}",
      "variant": "{选填，string}",
      "temperature": "{选填，number}",
      "top_p": "{选填，number}",
      "prompt": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      },
      "disable": "{选填，boolean}",
      "description": "{选填，string}",
      "mode": "{选填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{选填，boolean}",
      "options": {},
      "color": "{选填，Hex color code (e.g., #FF5733) or theme color (e.g., primary)，string | \"primary\"|\"secondary\"|\"accent\"|\"success\"|\"warning\"|\"error\"|\"info\"}",
      "steps": "{选填，integer}",
      "maxSteps": "{选填，integer}",
      "permission": "{选填，PermissionConfig 对象}"
    }
  },
  "provider": {
    "{键}": {
      "api": "{选填，string}",
      "name": "{选填，string}",
      "env": [
        "{string}"
      ],
      "id": "{选填，string}",
      "npm": "{选填，string}",
      "whitelist": [
        "{string}"
      ],
      "blacklist": [
        "{string}"
      ],
      "options": {
        "apiKey": "{选填，string}",
        "baseURL": "{选填，string}",
        "enterpriseUrl": "{选填，string}",
        "setCacheKey": "{选填，boolean}",
        "timeout": "{选填，Timeout in milliseconds for full requests to this provider. Set to false to disable timeout.，integer | false}",
        "headerTimeout": "{选填，Timeout in milliseconds to wait for response headers. Provider integrations may set defaults. Set to false to disable timeout.，integer | false}",
        "chunkTimeout": "{选填，integer}"
      },
      "models": {
        "{键}": {
          "id": "{选填，string}",
          "name": "{选填，string}",
          "family": "{选填，string}",
          "release_date": "{选填，string}",
          "attachment": "{选填，boolean}",
          "reasoning": "{选填，boolean}",
          "temperature": "{选填，boolean}",
          "tool_call": "{选填，boolean}",
          "interleaved": "{选填，true | object}",
          "cost": {
            "input": "{必填，number}",
            "output": "{必填，number}",
            "cache_read": "{选填，number}",
            "cache_write": "{选填，number}",
            "context_over_200k": {
              "input": "{必填，number}",
              "output": "{必填，number}",
              "cache_read": "{选填，number}",
              "cache_write": "{选填，number}"
            }
          },
          "limit": {
            "context": "{必填，number}",
            "input": "{选填，number}",
            "output": "{必填，number}"
          },
          "modalities": {
            "input": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ],
            "output": [
              "{可选值: 'text', 'audio', 'image', 'video', 'pdf'}"
            ]
          },
          "experimental": "{选填，boolean}",
          "status": "{选填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
          "provider": {
            "npm": "{选填，string}",
            "api": "{选填，string}"
          },
          "options": {},
          "headers": {
            "{键}": "{string}"
          },
          "variants": {
            "{键}": {
              "disabled": "{选填，boolean}"
            }
          }
        }
      }
    }
  },
  "mcp": {
    "{键}": {
      "type": "{必填，Type of MCP server connection，可选值: 'local'}",
      "command": [
        "{string}"
      ],
      "cwd": "{选填，string}",
      "environment": {
        "{键}": "{string}"
      },
      "enabled": "{选填，boolean}",
      "timeout": "{选填，integer}"
    }
  },
  "formatter": "{选填，Enable or configure formatters. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "lsp": "{选填，Enable or configure LSP servers. Omit or set to false to disable, true to enable built-ins, or an object to enable built-ins with overrides.，boolean | object}",
  "instructions": [
    "{string}"
  ],
  "layout": "{选填，LayoutConfig 对象}",
  "permission": "{选填，PermissionConfig 对象}",
  "tools": {
    "{键}": "{boolean}"
  },
  "attachment": {
    "image": {
      "auto_resize": "{选填，boolean}",
      "max_width": "{选填，integer}",
      "max_height": "{选填，integer}",
      "max_base64_bytes": "{选填，integer}"
    }
  },
  "enterprise": {
    "url": "{选填，string}"
  },
  "tool_output": {
    "max_lines": "{选填，integer}",
    "max_bytes": "{选填，integer}"
  },
  "compaction": {
    "auto": "{选填，boolean}",
    "prune": "{选填，boolean}",
    "tail_turns": "{选填，integer}",
    "preserve_recent_tokens": "{选填，integer}",
    "reserved": "{选填，integer}"
  },
  "experimental": {
    "disable_paste_summary": "{选填，boolean}",
    "batch_tool": "{选填，boolean}",
    "openTelemetry": "{选填，boolean}",
    "primary_tools": [
      "{string}"
    ],
    "continue_loop_on_deny": "{选填，boolean}",
    "mcp_timeout": "{选填，integer}",
    "policies": [
      {
        "action": "{必填，可选值: 'provider.use'}",
        "effect": "{必填，PolicyEffect 对象}",
        "resource": "{必填，string}"
      }
    ]
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Dispose instance

**operationId**: `global.dispose`

> Clean up and dispose all OpenCode instances, releasing all resources.

#### 请求

```
POST {baseUrl}/global/dispose
```

#### 响应

**HTTP 200** Global disposed

```
Content-Type: application/json
```

```json
"{Global disposed，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get global events

**operationId**: `global.event`

> Subscribe to global events from the OpenCode system using server-sent events.

#### 请求

```
GET {baseUrl}/global/event
```

#### 响应

**HTTP 200** Event stream

```
Content-Type: text/event-stream
```

```json
{
  "directory": "{必填，string}",
  "project": "{选填，string}",
  "workspace": "{选填，string}",
  "payload": {
    "id": "{必填，string}",
    "type": "{必填，可选值: 'models-dev.refreshed'}",
    "properties": {}
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get health

**operationId**: `global.health`

> Get health information about the OpenCode server.

#### 请求

```
GET {baseUrl}/global/health
```

#### 响应

**HTTP 200** Health information

```
Content-Type: application/json
```

```json
{
  "healthy": "{必填，可选值: true}",
  "version": "{必填，string}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Upgrade opencode

**operationId**: `global.upgrade`

> Upgrade opencode to the specified version or latest if not specified.

#### 请求

```
POST {baseUrl}/global/upgrade
Content-Type: application/json
```

**请求体** (`application/json`):

```json
{
  "target": "{选填，string}"
}
```

#### 响应

**HTTP 200** Upgrade result

```
Content-Type: application/json
```

```json
{
  "success": "{必填，可选值: true}",
  "version": "{必填，string}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

## instance

### List agents

**operationId**: `app.agents`

> Get a list of all available AI agents in the OpenCode system.

#### 请求

```
GET {baseUrl}/agent?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of agents

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "description": "{选填，string}",
    "mode": "{必填，可选值: 'subagent', 'primary', 'all'}",
    "native": "{选填，boolean}",
    "hidden": "{选填，boolean}",
    "topP": "{选填，number}",
    "temperature": "{选填，number}",
    "color": "{选填，string}",
    "permission": [
      "{PermissionRule 对象}"
    ],
    "model": {
      "modelID": "{必填，string}",
      "providerID": "{必填，string}"
    },
    "variant": "{选填，string}",
    "prompt": "{选填，string}",
    "options": {},
    "steps": "{选填，number}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### List commands

**operationId**: `command.list`

> Get a list of all available commands in the OpenCode system.

#### 请求

```
GET {baseUrl}/command?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of commands

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "description": "{选填，string}",
    "agent": "{选填，string}",
    "model": "{选填，string}",
    "source": "{选填，可选值: 'command', 'mcp', 'skill'}",
    "template": "{必填，string}",
    "subtask": "{选填，boolean}",
    "hints": [
      "{string}"
    ]
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get formatter status

**operationId**: `formatter.status`

> Get formatter status

#### 请求

```
GET {baseUrl}/formatter?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Formatter status

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "extensions": [
      "{string}"
    ],
    "enabled": "{必填，boolean}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Dispose instance

**operationId**: `instance.dispose`

> Clean up and dispose the current OpenCode instance, releasing all resources.

#### 请求

```
POST {baseUrl}/instance/dispose?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Instance disposed

```
Content-Type: application/json
```

```json
"{Instance disposed，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get LSP status

**operationId**: `lsp.status`

> Get LSP server status

#### 请求

```
GET {baseUrl}/lsp?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** LSP server status

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "name": "{必填，string}",
    "root": "{必填，string}",
    "status": "{必填，可选值: 'connected', 'error'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get paths

**operationId**: `path.get`

> Retrieve the current working directory and related path information for the OpenCode instance.

#### 请求

```
GET {baseUrl}/path?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Path

```
Content-Type: application/json
```

```json
{
  "home": "{必填，string}",
  "state": "{必填，string}",
  "config": "{必填，string}",
  "worktree": "{必填，string}",
  "directory": "{必填，string}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### List skills

**operationId**: `app.skills`

> Get a list of all available skills in the OpenCode system.

#### 请求

```
GET {baseUrl}/skill?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of skills

```
Content-Type: application/json
```

```json
[
  {
    "name": "{必填，string}",
    "description": "{选填，string}",
    "location": "{必填，string}",
    "content": "{必填，string}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get VCS info

**operationId**: `vcs.get`

> Retrieve version control system (VCS) information for the current project, such as git branch.

#### 请求

```
GET {baseUrl}/vcs?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** VCS info

```
Content-Type: application/json
```

```json
{
  "branch": "{选填，string}",
  "default_branch": "{选填，string}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Apply VCS patch

**operationId**: `vcs.apply`

> Apply a raw patch to the current working tree.

#### 请求

```
POST {baseUrl}/vcs/apply?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "patch": "{必填，string}"
}
```

#### 响应

**HTTP 200** VCS patch applied

```
Content-Type: application/json
```

```json
{
  "applied": "{必填，boolean}"
}
```

**HTTP 400** VcsApplyError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'VcsApplyError'}",
  "data": {
    "message": "{必填，string}",
    "reason": "{必填，可选值: 'non-git', 'not-clean'}"
  }
}
```

---

### Get VCS diff

**operationId**: `vcs.diff`

> Retrieve the current git diff for the working tree or against the default branch.

#### 请求

```
GET {baseUrl}/vcs/diff?directory={directory}&workspace={workspace}&mode={mode}&context={context}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `mode` | query | 是 | "git"\|"branch" |  |
| `context` | query | 否 | integer |  |

#### 响应

**HTTP 200** VCS diff

```
Content-Type: application/json
```

```json
[
  {
    "file": "{必填，string}",
    "patch": "{选填，string}",
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get raw VCS diff

**operationId**: `vcs.diff.raw`

> Retrieve a raw patch for current uncommitted changes.

#### 请求

```
GET {baseUrl}/vcs/diff/raw?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Raw VCS diff

```
Content-Type: text/x-diff; charset=utf-8
```

```json
"{string}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get VCS status

**operationId**: `vcs.status`

> Retrieve changed files in the current working tree without patches.

#### 请求

```
GET {baseUrl}/vcs/status?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** VCS status

```
Content-Type: application/json
```

```json
[
  {
    "file": "{必填，string}",
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "status": "{必填，可选值: 'added', 'deleted', 'modified'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## integrations

### List integrations

**operationId**: `v2.integration.list`

> Retrieve available integrations and their authentication methods.

#### 请求

```
GET {baseUrl}/api/integration?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "name": "{必填，string}",
      "methods": [
        "{IntegrationMethod 对象}"
      ],
      "connections": [
        "{ConnectionInfo 对象}"
      ]
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Get integration

**operationId**: `v2.integration.get`

> Retrieve one integration and its authentication methods.

#### 请求

```
GET {baseUrl}/api/integration/{integrationID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `integrationID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "id": "{必填，string}",
    "name": "{必填，string}",
    "methods": [
      "{IntegrationOAuthMethod 对象}"
    ],
    "connections": [
      "{ConnectionCredentialInfo 对象}"
    ]
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Connect with key

**operationId**: `v2.integration.connect.key`

> Run a key authentication method and store the resulting credential.

#### 请求

```
POST {baseUrl}/api/integration/{integrationID}/connect/key?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `integrationID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "key": "{必填，string}",
  "label": "{选填，string}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Begin OAuth connection

**operationId**: `v2.integration.connect.oauth`

> Start an OAuth attempt and return the authorization details.

#### 请求

```
POST {baseUrl}/api/integration/{integrationID}/connect/oauth?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `integrationID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "methodID": "{必填，string}",
  "inputs": {
    "{键}": "{string}"
  },
  "label": "{选填，string}"
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "attemptID": "{必填，string}",
    "url": "{必填，string}",
    "instructions": "{必填，string}",
    "mode": "{必填，可选值: 'auto', 'code'}",
    "time": {
      "created": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}",
      "expires": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}"
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Cancel OAuth connection

**operationId**: `v2.integration.attempt.cancel`

> Cancel an OAuth attempt and release its resources.

#### 请求

```
DELETE {baseUrl}/api/integration/attempt/{attemptID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `attemptID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Get OAuth attempt status

**operationId**: `v2.integration.attempt.status`

> Poll the current status of an OAuth attempt.

#### 请求

```
GET {baseUrl}/api/integration/attempt/{attemptID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `attemptID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "status": "{必填，可选值: 'pending'}",
    "time": {
      "created": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}",
      "expires": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}"
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Complete OAuth connection

**operationId**: `v2.integration.attempt.complete`

> Complete a code-based OAuth attempt and store the resulting credential.

#### 请求

```
POST {baseUrl}/api/integration/attempt/{attemptID}/complete?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `attemptID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "code": "{选填，string}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## mcp

### Get MCP status

**operationId**: `mcp.status`

> Get the status of all Model Context Protocol (MCP) servers.

#### 请求

```
GET {baseUrl}/mcp?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** MCP server status

```
Content-Type: application/json
```

```json
{
  "{键}": {
    "status": "{必填，可选值: 'connected'}"
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Add MCP server

**operationId**: `mcp.add`

> Dynamically add a new Model Context Protocol (MCP) server to the system.

#### 请求

```
POST {baseUrl}/mcp?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "name": "{必填，string}",
  "config": {
    "type": "{必填，Type of MCP server connection，可选值: 'local'}",
    "command": [
      "{string}"
    ],
    "cwd": "{选填，string}",
    "environment": {
      "{键}": "{string}"
    },
    "enabled": "{选填，boolean}",
    "timeout": "{选填，integer}"
  }
}
```

#### 响应

**HTTP 200** MCP server added successfully

```
Content-Type: application/json
```

```json
{
  "{键}": {
    "status": "{必填，可选值: 'connected'}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Remove MCP OAuth

**operationId**: `mcp.auth.remove`

> Remove OAuth credentials for an MCP server.

#### 请求

```
DELETE {baseUrl}/mcp/{name}/auth?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** OAuth credentials removed

```
Content-Type: application/json
```

```json
{
  "success": "{必填，可选值: true}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Start MCP OAuth

**operationId**: `mcp.auth.start`

> Start OAuth authentication flow for a Model Context Protocol (MCP) server.

#### 请求

```
POST {baseUrl}/mcp/{name}/auth?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** OAuth flow started

```
Content-Type: application/json
```

```json
{
  "authorizationUrl": "{必填，string}",
  "oauthState": "{必填，string}"
}
```

**HTTP 400** McpUnsupportedOAuthError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "error": "{必填，string}"
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Authenticate MCP OAuth

**operationId**: `mcp.auth.authenticate`

> Start OAuth flow and wait for callback (opens browser).

#### 请求

```
POST {baseUrl}/mcp/{name}/auth/authenticate?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** OAuth authentication completed

```
Content-Type: application/json
```

```json
{
  "status": "{必填，可选值: 'connected'}"
}
```

**HTTP 400** McpUnsupportedOAuthError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "error": "{必填，string}"
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Complete MCP OAuth

**operationId**: `mcp.auth.callback`

> Complete OAuth authentication for a Model Context Protocol (MCP) server using the authorization code.

#### 请求

```
POST {baseUrl}/mcp/{name}/auth/callback?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "code": "{必填，string}"
}
```

#### 响应

**HTTP 200** OAuth authentication completed

```
Content-Type: application/json
```

```json
{
  "status": "{必填，可选值: 'connected'}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### mcp.connect

**operationId**: `mcp.connect`

> Connect an MCP server.

#### 请求

```
POST {baseUrl}/mcp/{name}/connect?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** MCP server connected successfully

```
Content-Type: application/json
```

```json
"{MCP server connected successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### mcp.disconnect

**operationId**: `mcp.disconnect`

> Disconnect an MCP server.

#### 请求

```
POST {baseUrl}/mcp/{name}/disconnect?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `name` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** MCP server disconnected successfully

```
Content-Type: application/json
```

```json
"{MCP server disconnected successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** McpServerNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'McpServerNotFoundError'}",
  "name": "{必填，string}",
  "message": "{必填，string}"
}
```

---

## messages

### Get session messages

**operationId**: `v2.session.messages`

> Retrieve projected messages for a session. Items keep the requested order across pages; use cursor.next or cursor.previous to move through the ordered timeline.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/message?limit={limit}&order={order}&cursor={cursor}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `limit` | query | 否 | number |  |
| `order` | query | 否 | "asc"\|"desc" |  |
| `cursor` | query | 否 | string |  |

#### 响应

**HTTP 200** SessionMessagesResponse

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "metadata": {},
      "time": {
        "created": "{必填，number}"
      },
      "type": "{必填，可选值: 'agent-switched'}",
      "agent": "{必填，string}"
    }
  ],
  "cursor": {
    "previous": "{选填，string}",
    "next": "{选填，string}"
  }
}
```

**HTTP 400** InvalidCursorError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidCursorError'}",
  "message": "{必填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 500** UnknownError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnknownError'}",
  "message": "{必填，string}",
  "ref": "{选填，string}"
}
```

---

## models

### List models

**operationId**: `v2.model.list`

> Retrieve available models ordered by release date.

#### 请求

```
GET {baseUrl}/api/model?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "family": "{选填，string}",
      "name": "{必填，string}",
      "api": {
        "id": "{必填，string}",
        "type": "{必填，可选值: 'aisdk'}",
        "package": "{必填，string}",
        "url": "{选填，string}",
        "settings": {}
      },
      "capabilities": {
        "tools": "{必填，boolean}",
        "input": [
          "{string}"
        ],
        "output": [
          "{string}"
        ]
      },
      "request": {
        "headers": {
          "{键}": "{string}"
        },
        "body": {},
        "variant": "{选填，string}"
      },
      "variants": [
        {
          "id": "{必填，string}",
          "headers": {
            "{键}": "{string}"
          },
          "body": {}
        }
      ],
      "time": {
        "released": "{必填，number}"
      },
      "cost": [
        "{ModelCost 对象}"
      ],
      "status": "{必填，可选值: 'alpha', 'beta', 'deprecated', 'active'}",
      "enabled": "{必填，boolean}",
      "limit": {
        "context": "{必填，integer}",
        "input": "{选填，integer}",
        "output": "{必填，integer}"
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 503** ServiceUnavailableError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ServiceUnavailableError'}",
  "message": "{必填，string}",
  "service": "{选填，string}"
}
```

---

## opencode HttpApi

### List agents

**operationId**: `v2.agent.list`

> Retrieve currently registered agents.

#### 请求

```
GET {baseUrl}/api/agent?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "model": {
        "id": "{必填，string}",
        "providerID": "{必填，string}",
        "variant": "{选填，string}"
      },
      "request": {
        "headers": {
          "{键}": "{string}"
        },
        "body": {}
      },
      "system": "{选填，string}",
      "description": "{选填，string}",
      "mode": "{必填，可选值: 'subagent', 'primary', 'all'}",
      "hidden": "{必填，boolean}",
      "color": "{选填，AgentColor 对象}",
      "steps": "{选填，integer}",
      "permissions": [
        "{PermissionV2Rule 对象}"
      ]
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Remove credential

**operationId**: `v2.credential.remove`

> Remove a stored integration credential.

#### 请求

```
DELETE {baseUrl}/api/credential/{credentialID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `credentialID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Update credential

**operationId**: `v2.credential.update`

> Update a stored credential label.

#### 请求

```
PATCH {baseUrl}/api/credential/{credentialID}?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `credentialID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "label": "{必填，string}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Check server health

**operationId**: `v2.health.get`

> Check whether the API server is ready to accept requests.

#### 请求

```
GET {baseUrl}/api/health
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "healthy": "{必填，可选值: true}"
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Get location

**operationId**: `v2.location.get`

> Resolve the requested location or the server default location.

#### 请求

```
GET {baseUrl}/api/location?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Location.Info

```
Content-Type: application/json
```

```json
{
  "directory": "{必填，string}",
  "workspaceID": "{选填，string}",
  "project": {
    "id": "{必填，string}",
    "directory": "{必填，string}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## permission

### List pending permissions

**operationId**: `permission.list`

> Get all pending permission requests across all sessions.

#### 请求

```
GET {baseUrl}/permission?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of pending permissions

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "permission": "{必填，string}",
    "patterns": [
      "{string}"
    ],
    "metadata": {},
    "always": [
      "{string}"
    ],
    "tool": {
      "messageID": "{必填，string}",
      "callID": "{必填，string}"
    }
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Respond to permission request

**operationId**: `permission.reply`

> Approve or deny a permission request from the AI assistant.

#### 请求

```
POST {baseUrl}/permission/{requestID}/reply?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `requestID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "reply": "{必填，可选值: 'once', 'always', 'reject'}",
  "message": "{选填，string}"
}
```

#### 响应

**HTTP 200** Permission processed successfully

```
Content-Type: application/json
```

```json
"{Permission processed successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** PermissionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PermissionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

## permissions

### List pending permission requests

**operationId**: `v2.permission.request.list`

> Retrieve pending permission requests for a location.

#### 请求

```
GET {baseUrl}/api/permission/request?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "action": "{必填，string}",
      "resources": [
        "{string}"
      ],
      "save": [
        "{string}"
      ],
      "metadata": {},
      "source": {
        "type": "{必填，可选值: 'tool'}",
        "messageID": "{必填，string}",
        "callID": "{必填，string}"
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### List saved permissions

**operationId**: `v2.permission.saved.list`

> Retrieve saved permissions, optionally filtered by project.

#### 请求

```
GET {baseUrl}/api/permission/saved?projectID={projectID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | query | 否 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "projectID": "{必填，string}",
      "action": "{必填，string}",
      "resource": "{必填，string}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Remove saved permission

**operationId**: `v2.permission.saved.remove`

> Remove a saved permission by ID.

#### 请求

```
DELETE {baseUrl}/api/permission/saved/{id}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `id` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### List session permission requests

**operationId**: `v2.session.permission.list`

> Retrieve pending permission requests owned by a session.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/permission
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "action": "{必填，string}",
      "resources": [
        "{string}"
      ],
      "save": [
        "{string}"
      ],
      "metadata": {},
      "source": {
        "type": "{必填，可选值: 'tool'}",
        "messageID": "{必填，string}",
        "callID": "{必填，string}"
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Create permission request

**operationId**: `v2.session.permission.create`

> Evaluate and, when approval is required, create a permission request for a session.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/permission
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "id": "{选填，string}",
  "action": "{必填，string}",
  "resources": [
    "{string}"
  ],
  "save": [
    "{string}"
  ],
  "metadata": {},
  "source": {
    "type": "{必填，可选值: 'tool'}",
    "messageID": "{必填，string}",
    "callID": "{必填，string}"
  },
  "agent": "{选填，string}"
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "id": "{必填，string}",
    "effect": "{必填，PermissionV2Effect 对象}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get permission request

**operationId**: `v2.session.permission.get`

> Retrieve a pending permission request owned by a session.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/permission/{requestID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `requestID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "action": "{必填，string}",
    "resources": [
      "{string}"
    ],
    "save": [
      "{string}"
    ],
    "metadata": {},
    "source": {
      "type": "{必填，可选值: 'tool'}",
      "messageID": "{必填，string}",
      "callID": "{必填，string}"
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError | PermissionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PermissionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Reply to pending permission request

**operationId**: `v2.session.permission.reply`

> Respond to a pending permission request owned by a session.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/permission/{requestID}/reply
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `requestID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "reply": "{必填，PermissionV2Reply 对象}",
  "message": "{选填，string}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError | PermissionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PermissionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

## project

### List all projects

**operationId**: `project.list`

> Get a list of projects that have been opened with OpenCode.

#### 请求

```
GET {baseUrl}/project?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of projects

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "worktree": "{必填，string}",
    "vcs": "{选填，ProjectVcs 对象}",
    "name": "{选填，string}",
    "icon": {
      "url": "{选填，string}",
      "override": "{选填，string}",
      "color": "{选填，string}"
    },
    "commands": {
      "start": "{选填，Startup script to run when creating a new workspace (worktree)，string}"
    },
    "time": {
      "created": "{必填，integer}",
      "updated": "{必填，integer}",
      "initialized": "{选填，integer}"
    },
    "sandboxes": [
      "{string}"
    ]
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Update project

**operationId**: `project.update`

> Update project properties such as name, icon, and commands.

#### 请求

```
PATCH {baseUrl}/project/{projectID}?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "name": "{选填，string}",
  "icon": {
    "url": "{选填，string}",
    "override": "{选填，string}",
    "color": "{选填，string}"
  },
  "commands": {
    "start": "{选填，Startup script to run when creating a new workspace (worktree)，string}"
  }
}
```

#### 响应

**HTTP 200** Updated project information

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "worktree": "{必填，string}",
  "vcs": "{选填，ProjectVcs 对象}",
  "name": "{选填，string}",
  "icon": {
    "url": "{选填，string}",
    "override": "{选填，string}",
    "color": "{选填，string}"
  },
  "commands": {
    "start": "{选填，Startup script to run when creating a new workspace (worktree)，string}"
  },
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "initialized": "{选填，integer}"
  },
  "sandboxes": [
    "{string}"
  ]
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** ProjectNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ProjectNotFoundError'}",
  "projectID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### List project directories

**operationId**: `project.directories`

> List known local absolute directories for a project.

#### 请求

```
GET {baseUrl}/project/{projectID}/directories?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Project directories

```
Content-Type: application/json
```

```json
[
  {
    "directory": "{必填，string}",
    "strategy": "{选填，string}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get current project

**operationId**: `project.current`

> Retrieve the currently active project that OpenCode is working with.

#### 请求

```
GET {baseUrl}/project/current?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Current project information

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "worktree": "{必填，string}",
  "vcs": "{选填，ProjectVcs 对象}",
  "name": "{选填，string}",
  "icon": {
    "url": "{选填，string}",
    "override": "{选填，string}",
    "color": "{选填，string}"
  },
  "commands": {
    "start": "{选填，Startup script to run when creating a new workspace (worktree)，string}"
  },
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "initialized": "{选填，integer}"
  },
  "sandboxes": [
    "{string}"
  ]
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Initialize git repository

**operationId**: `project.initGit`

> Create a git repository for the current project and return the refreshed project info.

#### 请求

```
POST {baseUrl}/project/git/init?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Project information after git initialization

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "worktree": "{必填，string}",
  "vcs": "{选填，ProjectVcs 对象}",
  "name": "{选填，string}",
  "icon": {
    "url": "{选填，string}",
    "override": "{选填，string}",
    "color": "{选填，string}"
  },
  "commands": {
    "start": "{选填，Startup script to run when creating a new workspace (worktree)，string}"
  },
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "initialized": "{选填，integer}"
  },
  "sandboxes": [
    "{string}"
  ]
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## projectCopy

### v2.projectCopy.remove

**operationId**: `v2.projectCopy.remove`

#### 请求

```
DELETE {baseUrl}/experimental/project/{projectID}/copy?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "directory": "{必填，string}",
  "force": "{必填，boolean}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** ProjectCopyError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'ProjectCopyError'}",
  "data": {
    "message": "{必填，string}",
    "forceRequired": "{选填，boolean}"
  }
}
```

---

### v2.projectCopy.create

**operationId**: `v2.projectCopy.create`

#### 请求

```
POST {baseUrl}/experimental/project/{projectID}/copy?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "strategy": "{必填，string}",
  "directory": "{必填，string}",
  "name": "{选填，string}"
}
```

#### 响应

**HTTP 200** ProjectCopy.Copy

```
Content-Type: application/json
```

```json
{
  "directory": "{必填，string}"
}
```

**HTTP 400** ProjectCopyError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'ProjectCopyError'}",
  "data": {
    "message": "{必填，string}",
    "forceRequired": "{选填，boolean}"
  }
}
```

---

### Generate project copy name

**operationId**: `experimental.projectCopy.generateName`

> Generate a short name for a project copy from task context.

#### 请求

```
POST {baseUrl}/experimental/project/{projectID}/copy/generate-name?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "context": "{选填，string}"
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "name": "{必填，string}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### v2.projectCopy.refresh

**operationId**: `v2.projectCopy.refresh`

#### 请求

```
POST {baseUrl}/experimental/project/{projectID}/copy/refresh?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `projectID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** ProjectCopyError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'ProjectCopyError'}",
  "data": {
    "message": "{必填，string}",
    "forceRequired": "{选填，boolean}"
  }
}
```

---

## provider

### List providers

**operationId**: `provider.list`

> Get a list of all available AI providers, including both available and connected ones.

#### 请求

```
GET {baseUrl}/provider?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of providers

```
Content-Type: application/json
```

```json
{
  "all": [
    {
      "id": "{必填，string}",
      "name": "{必填，string}",
      "source": "{必填，可选值: 'env', 'config', 'custom', 'api'}",
      "env": [
        "{string}"
      ],
      "key": "{选填，string}",
      "options": {},
      "models": {
        "{键}": "{Model 对象}"
      }
    }
  ],
  "default": {
    "{键}": "{string}"
  },
  "connected": [
    "{string}"
  ]
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Start OAuth authorization

**operationId**: `provider.oauth.authorize`

> Start the OAuth authorization flow for a provider.

#### 请求

```
POST {baseUrl}/provider/{providerID}/oauth/authorize?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `providerID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "method": "{必填，Auth method index，number}",
  "inputs": {
    "{键}": "{string}"
  }
}
```

#### 响应

**HTTP 200** Authorization URL and method

```
Content-Type: application/json
```

```json
{
  "url": "{必填，string}",
  "method": "{必填，可选值: 'auto', 'code'}",
  "instructions": "{必填，string}"
}
```

**HTTP 400** ProviderAuthError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest', 'ProviderAuthOauthMissing', 'ProviderAuthOauthCodeMissing', 'ProviderAuthOauthCallbackFailed', 'ProviderAuthValidationFailed'}",
  "data": {
    "providerID": "{选填，string}",
    "field": "{选填，string}",
    "message": "{选填，string}",
    "kind": "{选填，string}"
  }
}
```

---

### Handle OAuth callback

**operationId**: `provider.oauth.callback`

> Handle the OAuth callback from a provider after user authorization.

#### 请求

```
POST {baseUrl}/provider/{providerID}/oauth/callback?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `providerID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "method": "{必填，Auth method index，number}",
  "code": "{选填，string}"
}
```

#### 响应

**HTTP 200** OAuth callback processed successfully

```
Content-Type: application/json
```

```json
"{OAuth callback processed successfully，boolean}"
```

**HTTP 400** ProviderAuthError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest', 'ProviderAuthOauthMissing', 'ProviderAuthOauthCodeMissing', 'ProviderAuthOauthCallbackFailed', 'ProviderAuthValidationFailed'}",
  "data": {
    "providerID": "{选填，string}",
    "field": "{选填，string}",
    "message": "{选填，string}",
    "kind": "{选填，string}"
  }
}
```

---

### Get provider auth methods

**operationId**: `provider.auth`

> Retrieve available authentication methods for all AI providers.

#### 请求

```
GET {baseUrl}/provider/auth?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Provider auth methods

```
Content-Type: application/json
```

```json
{
  "{键}": [
    {
      "type": "{必填，可选值: 'oauth', 'api'}",
      "label": "{必填，string}",
      "prompts": [
        {
          "type": "{必填，可选值: 'text'}",
          "key": "{必填，string}",
          "message": "{必填，string}",
          "placeholder": "{选填，string}",
          "when": {
            "key": "{必填，string}",
            "op": "{必填，可选值: 'eq', 'neq'}",
            "value": "{必填，string}"
          }
        }
      ]
    }
  ]
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## providers

### List providers

**operationId**: `v2.provider.list`

> Retrieve active AI providers so clients can show provider availability and configuration.

#### 请求

```
GET {baseUrl}/api/provider?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "integrationID": "{选填，string}",
      "name": "{必填，string}",
      "disabled": "{选填，boolean}",
      "api": "{必填，ProviderApi 对象}",
      "request": {
        "headers": {
          "{键}": "{string}"
        },
        "body": {}
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 503** ServiceUnavailableError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ServiceUnavailableError'}",
  "message": "{必填，string}",
  "service": "{选填，string}"
}
```

---

### Get provider

**operationId**: `v2.provider.get`

> Retrieve a single AI provider so clients can inspect its availability and endpoint settings.

#### 请求

```
GET {baseUrl}/api/provider/{providerID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `providerID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "id": "{必填，string}",
    "integrationID": "{选填，string}",
    "name": "{必填，string}",
    "disabled": "{选填，boolean}",
    "api": {
      "type": "{必填，可选值: 'aisdk'}",
      "package": "{必填，string}",
      "url": "{选填，string}",
      "settings": {}
    },
    "request": {
      "headers": {
        "{键}": "{string}"
      },
      "body": {}
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** ProviderNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ProviderNotFoundError'}",
  "providerID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 503** ServiceUnavailableError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ServiceUnavailableError'}",
  "message": "{必填，string}",
  "service": "{选填，string}"
}
```

---

## pty

### List PTY sessions

**operationId**: `v2.pty.list`

> List PTY sessions for a location, including exited sessions retained until removal.

#### 请求

```
GET {baseUrl}/api/pty?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "title": "{必填，string}",
      "command": "{必填，string}",
      "args": [
        "{string}"
      ],
      "cwd": "{必填，string}",
      "status": "{必填，可选值: 'running', 'exited'}",
      "pid": "{必填，integer}",
      "exitCode": "{选填，integer}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Create PTY session

**operationId**: `v2.pty.create`

> Create a pseudo-terminal session for a location.

#### 请求

```
POST {baseUrl}/api/pty?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "command": "{选填，string}",
  "args": [
    "{string}"
  ],
  "cwd": "{选填，string}",
  "title": "{选填，string}",
  "env": {
    "{键}": "{string}"
  }
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "id": "{必填，string}",
    "title": "{必填，string}",
    "command": "{必填，string}",
    "args": [
      "{string}"
    ],
    "cwd": "{必填，string}",
    "status": "{必填，可选值: 'running', 'exited'}",
    "pid": "{必填，integer}",
    "exitCode": "{选填，integer}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Remove PTY session

**operationId**: `v2.pty.remove`

> Terminate and remove one PTY session.

#### 请求

```
DELETE {baseUrl}/api/pty/{ptyID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get PTY session

**operationId**: `v2.pty.get`

> Get one PTY session, including its exit code once exited.

#### 请求

```
GET {baseUrl}/api/pty/{ptyID}?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "id": "{必填，string}",
    "title": "{必填，string}",
    "command": "{必填，string}",
    "args": [
      "{string}"
    ],
    "cwd": "{必填，string}",
    "status": "{必填，可选值: 'running', 'exited'}",
    "pid": "{必填，integer}",
    "exitCode": "{选填，integer}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Update PTY session

**operationId**: `v2.pty.update`

> Update the title or viewport size of one PTY session.

#### 请求

```
PUT {baseUrl}/api/pty/{ptyID}?location={location}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

**请求体** (`application/json`):

```json
{
  "title": "{选填，string}",
  "size": {
    "rows": "{必填，integer}",
    "cols": "{必填，integer}"
  }
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "id": "{必填，string}",
    "title": "{必填，string}",
    "command": "{必填，string}",
    "args": [
      "{string}"
    ],
    "cwd": "{必填，string}",
    "status": "{必填，可选值: 'running', 'exited'}",
    "pid": "{必填，integer}",
    "exitCode": "{选填，integer}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Connect to PTY session

**operationId**: `v2.pty.connect`

> Establish a WebSocket connection streaming PTY output and accepting terminal input.

#### 请求

```
GET {baseUrl}/api/pty/{ptyID}/connect?location[directory]={location[directory]}&location[workspace]={location[workspace]}&cursor={cursor}&ticket={ticket}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `location[directory]` | query | 否 | string |  |
| `location[workspace]` | query | 否 | string |  |
| `cursor` | query | 否 | string |  |
| `ticket` | query | 否 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
"{boolean}"
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 403** ForbiddenError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ForbiddenError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Create PTY WebSocket token

**operationId**: `v2.pty.connectToken`

> Create a short-lived single-use ticket for opening a PTY WebSocket connection.

#### 请求

```
POST {baseUrl}/api/pty/{ptyID}/connect-token?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": {
    "ticket": "{必填，string}",
    "expires_in": "{必填，integer}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 403** ForbiddenError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ForbiddenError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### List PTY sessions

**operationId**: `pty.list`

> Get a list of all active pseudo-terminal (PTY) sessions managed by OpenCode.

#### 请求

```
GET {baseUrl}/pty?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of sessions

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "title": "{必填，string}",
    "command": "{必填，string}",
    "args": [
      "{string}"
    ],
    "cwd": "{必填，string}",
    "status": "{必填，可选值: 'running', 'exited'}",
    "pid": "{必填，integer}",
    "exitCode": "{选填，integer}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Create PTY session

**operationId**: `pty.create`

> Create a new pseudo-terminal (PTY) session for running shell commands and processes.

#### 请求

```
POST {baseUrl}/pty?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "command": "{选填，string}",
  "args": [
    "{string}"
  ],
  "cwd": "{选填，string}",
  "title": "{选填，string}",
  "env": {
    "{键}": "{string}"
  }
}
```

#### 响应

**HTTP 200** Created session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "title": "{必填，string}",
  "command": "{必填，string}",
  "args": [
    "{string}"
  ],
  "cwd": "{必填，string}",
  "status": "{必填，可选值: 'running', 'exited'}",
  "pid": "{必填，integer}",
  "exitCode": "{选填，integer}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Remove PTY session

**operationId**: `pty.remove`

> Remove and terminate a specific pseudo-terminal (PTY) session.

#### 请求

```
DELETE {baseUrl}/pty/{ptyID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Session removed

```
Content-Type: application/json
```

```json
"{Session removed，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get PTY session

**operationId**: `pty.get`

> Retrieve detailed information about a specific pseudo-terminal (PTY) session.

#### 请求

```
GET {baseUrl}/pty/{ptyID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Session info

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "title": "{必填，string}",
  "command": "{必填，string}",
  "args": [
    "{string}"
  ],
  "cwd": "{必填，string}",
  "status": "{必填，可选值: 'running', 'exited'}",
  "pid": "{必填，integer}",
  "exitCode": "{选填，integer}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Update PTY session

**operationId**: `pty.update`

> Update properties of an existing pseudo-terminal (PTY) session.

#### 请求

```
PUT {baseUrl}/pty/{ptyID}?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "title": "{选填，string}",
  "size": {
    "rows": "{必填，integer}",
    "cols": "{必填，integer}"
  }
}
```

#### 响应

**HTTP 200** Updated session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "title": "{必填，string}",
  "command": "{必填，string}",
  "args": [
    "{string}"
  ],
  "cwd": "{必填，string}",
  "status": "{必填，可选值: 'running', 'exited'}",
  "pid": "{必填，integer}",
  "exitCode": "{选填，integer}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Connect to PTY session

**operationId**: `pty.connect`

> Establish a WebSocket connection to interact with a pseudo-terminal (PTY) session in real-time.

#### 请求

```
GET {baseUrl}/pty/{ptyID}/connect?directory={directory}&workspace={workspace}&cursor={cursor}&ticket={ticket}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `cursor` | query | 否 | string |  |
| `ticket` | query | 否 | string |  |

#### 响应

**HTTP 200** Connected session

```
Content-Type: application/json
```

```json
"{Connected session，boolean}"
```

**HTTP 403** Forbidden

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'Forbidden'}"
}
```

**HTTP 404** Not found

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Create PTY WebSocket token

**operationId**: `pty.connectToken`

> Create a short-lived ticket for opening a PTY WebSocket connection.

#### 请求

```
POST {baseUrl}/pty/{ptyID}/connect-token?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `ptyID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** WebSocket connect token

```
Content-Type: application/json
```

```json
{
  "ticket": "{必填，string}",
  "expires_in": "{必填，integer}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 403** PtyForbiddenError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyForbiddenError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** PtyNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'PtyNotFoundError'}",
  "ptyID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### List available shells

**operationId**: `pty.shells`

> Get a list of available shells on the system.

#### 请求

```
GET {baseUrl}/pty/shells?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of shells

```
Content-Type: application/json
```

```json
[
  {
    "path": "{必填，string}",
    "name": "{必填，string}",
    "acceptable": "{必填，boolean}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## question

### List pending questions

**operationId**: `question.list`

> Get all pending question requests across all sessions.

#### 请求

```
GET {baseUrl}/question?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of pending questions

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "questions": [
      {
        "question": "{必填，Complete question，string}",
        "header": "{必填，Very short label (max 30 chars)，string}",
        "options": [
          "{QuestionOption 对象}"
        ],
        "multiple": "{选填，boolean}",
        "custom": "{选填，boolean}"
      }
    ],
    "tool": {
      "messageID": "{必填，string}",
      "callID": "{必填，string}"
    }
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Reject question request

**operationId**: `question.reject`

> Reject a question request from the AI assistant.

#### 请求

```
POST {baseUrl}/question/{requestID}/reject?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `requestID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Question rejected successfully

```
Content-Type: application/json
```

```json
"{Question rejected successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** QuestionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'QuestionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Reply to question request

**operationId**: `question.reply`

> Provide answers to a question request from the AI assistant.

#### 请求

```
POST {baseUrl}/question/{requestID}/reply?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `requestID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "answers": [
    [
      "{string}"
    ]
  ]
}
```

#### 响应

**HTTP 200** Question answered successfully

```
Content-Type: application/json
```

```json
"{Question answered successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** QuestionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'QuestionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

## reference

### List references

**operationId**: `v2.reference.list`

> List references available in the requested location.

#### 请求

```
GET {baseUrl}/api/reference?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "name": "{必填，string}",
      "path": "{必填，string}",
      "description": "{选填，string}",
      "hidden": "{选填，boolean}",
      "source": "{必填，ReferenceSource 对象}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## session

### List sessions

**operationId**: `session.list`

> Get a list of all OpenCode sessions, sorted by most recently updated.

#### 请求

```
GET {baseUrl}/session?directory={directory}&workspace={workspace}&scope={scope}&path={path}&roots={roots}&start={start}&search={search}&limit={limit}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `scope` | query | 否 | "project" |  |
| `path` | query | 否 | string |  |
| `roots` | query | 否 | boolean \| "true"\|"false" |  |
| `start` | query | 否 | number |  |
| `search` | query | 否 | string |  |
| `limit` | query | 否 | number |  |

#### 响应

**HTTP 200** List of sessions

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "slug": "{必填，string}",
    "projectID": "{必填，string}",
    "workspaceID": "{选填，string}",
    "directory": "{必填，string}",
    "path": "{选填，string}",
    "parentID": "{选填，string}",
    "summary": {
      "additions": "{必填，number}",
      "deletions": "{必填，number}",
      "files": "{必填，number}",
      "diffs": [
        "{SnapshotFileDiff 对象}"
      ]
    },
    "cost": "{选填，number}",
    "tokens": {
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "share": {
      "url": "{必填，string}"
    },
    "title": "{必填，string}",
    "agent": "{选填，string}",
    "model": {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "version": "{必填，string}",
    "metadata": {},
    "time": {
      "created": "{必填，integer}",
      "updated": "{必填，integer}",
      "compacting": "{选填，integer}",
      "archived": "{选填，number}"
    },
    "permission": [
      "{PermissionRule 对象}"
    ],
    "revert": {
      "messageID": "{必填，string}",
      "partID": "{选填，string}",
      "snapshot": "{选填，string}",
      "diff": "{选填，string}"
    }
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Create session

**operationId**: `session.create`

> Create a new OpenCode session for interacting with AI assistants and managing conversations.

#### 请求

```
POST {baseUrl}/session?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "parentID": "{选填，string}",
  "title": "{选填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "metadata": {},
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "workspaceID": "{选填，string}"
}
```

#### 响应

**HTTP 200** Successfully created session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Delete session

**operationId**: `session.delete`

> Delete a session and permanently remove all associated data, including messages and history.

#### 请求

```
DELETE {baseUrl}/session/{sessionID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully deleted session

```
Content-Type: application/json
```

```json
"{Successfully deleted session，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Get session

**operationId**: `session.get`

> Retrieve detailed information about a specific OpenCode session.

#### 请求

```
GET {baseUrl}/session/{sessionID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Get session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Update session

**operationId**: `session.update`

> Update properties of an existing session, such as title or other metadata.

#### 请求

```
PATCH {baseUrl}/session/{sessionID}?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "title": "{选填，string}",
  "metadata": {},
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "time": {
    "archived": "{选填，number}"
  }
}
```

#### 响应

**HTTP 200** Successfully updated session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Abort session

**operationId**: `session.abort`

> Abort an active session and stop any ongoing AI processing or command execution.

#### 请求

```
POST {baseUrl}/session/{sessionID}/abort?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Aborted session

```
Content-Type: application/json
```

```json
"{Aborted session，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Get session children

**operationId**: `session.children`

> Retrieve all child sessions that were forked from the specified parent session.

#### 请求

```
GET {baseUrl}/session/{sessionID}/children?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** List of children

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "slug": "{必填，string}",
    "projectID": "{必填，string}",
    "workspaceID": "{选填，string}",
    "directory": "{必填，string}",
    "path": "{选填，string}",
    "parentID": "{选填，string}",
    "summary": {
      "additions": "{必填，number}",
      "deletions": "{必填，number}",
      "files": "{必填，number}",
      "diffs": [
        "{SnapshotFileDiff 对象}"
      ]
    },
    "cost": "{选填，number}",
    "tokens": {
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "share": {
      "url": "{必填，string}"
    },
    "title": "{必填，string}",
    "agent": "{选填，string}",
    "model": {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "version": "{必填，string}",
    "metadata": {},
    "time": {
      "created": "{必填，integer}",
      "updated": "{必填，integer}",
      "compacting": "{选填，integer}",
      "archived": "{选填，number}"
    },
    "permission": [
      "{PermissionRule 对象}"
    ],
    "revert": {
      "messageID": "{必填，string}",
      "partID": "{选填，string}",
      "snapshot": "{选填，string}",
      "diff": "{选填，string}"
    }
  }
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Send command

**operationId**: `session.command`

> Send a new command to a session for execution by the AI assistant.

#### 请求

```
POST {baseUrl}/session/{sessionID}/command?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{选填，string}",
  "agent": "{选填，string}",
  "model": "{选填，string}",
  "arguments": "{必填，string}",
  "command": "{必填，string}",
  "variant": "{选填，string}",
  "parts": [
    {
      "id": "{选填，string}",
      "type": "{必填，可选值: 'file'}",
      "mime": "{必填，string}",
      "filename": "{选填，string}",
      "url": "{必填，string}",
      "source": {
        "text": "{必填，FilePartSourceText 对象}",
        "type": "{必填，可选值: 'file'}",
        "path": "{必填，string}"
      }
    }
  ]
}
```

#### 响应

**HTTP 200** Created message

```
Content-Type: application/json
```

```json
{
  "info": {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "role": "{必填，可选值: 'assistant'}",
    "time": {
      "created": "{必填，integer}",
      "completed": "{选填，integer}"
    },
    "error": {
      "name": "{必填，可选值: 'ProviderAuthError'}",
      "data": {
        "providerID": "{必填，string}",
        "message": "{必填，string}"
      }
    },
    "parentID": "{必填，string}",
    "modelID": "{必填，string}",
    "providerID": "{必填，string}",
    "mode": "{必填，string}",
    "agent": "{必填，string}",
    "path": {
      "cwd": "{必填，string}",
      "root": "{必填，string}"
    },
    "summary": "{选填，boolean}",
    "cost": "{必填，number}",
    "tokens": {
      "total": "{选填，number}",
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "structured": "{选填，object}",
    "variant": "{选填，string}",
    "finish": "{选填，string}"
  },
  "parts": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "messageID": "{必填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Get message diff

**operationId**: `session.diff`

> Get the file changes (diff) that resulted from a specific user message in the session.

#### 请求

```
GET {baseUrl}/session/{sessionID}/diff?directory={directory}&workspace={workspace}&messageID={messageID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `messageID` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully retrieved diff

```
Content-Type: application/json
```

```json
[
  {
    "file": "{选填，string}",
    "patch": "{选填，string}",
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Fork session

**operationId**: `session.fork`

> Create a new session by forking an existing session at a specific message point.

#### 请求

```
POST {baseUrl}/session/{sessionID}/fork?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{选填，string}"
}
```

#### 响应

**HTTP 200** 200

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Initialize session

**operationId**: `session.init`

> Analyze the current application and create an AGENTS.md file with project-specific agent configurations.

#### 请求

```
POST {baseUrl}/session/{sessionID}/init?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "modelID": "{必填，string}",
  "providerID": "{必填，string}",
  "messageID": "{必填，string}"
}
```

#### 响应

**HTTP 200** 200

```
Content-Type: application/json
```

```json
"{200，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Get session messages

**operationId**: `session.messages`

> Retrieve all messages in a session, including user prompts and AI responses.

#### 请求

```
GET {baseUrl}/session/{sessionID}/message?directory={directory}&workspace={workspace}&limit={limit}&before={before}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |
| `limit` | query | 否 | integer |  |
| `before` | query | 否 | string |  |

#### 响应

**HTTP 200** List of messages

```
Content-Type: application/json
```

```json
[
  {
    "info": {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "role": "{必填，可选值: 'user'}",
      "time": {
        "created": "{必填，number}"
      },
      "format": "{选填，OutputFormat 对象}",
      "summary": {
        "title": "{选填，string}",
        "body": "{选填，string}",
        "diffs": [
          "{SnapshotFileDiff 对象}"
        ]
      },
      "agent": "{必填，string}",
      "model": {
        "providerID": "{必填，string}",
        "modelID": "{必填，string}",
        "variant": "{选填，string}"
      },
      "system": "{选填，string}",
      "tools": {
        "{键}": "{boolean}"
      }
    },
    "parts": [
      {
        "id": "{必填，string}",
        "sessionID": "{必填，string}",
        "messageID": "{必填，string}",
        "type": "{必填，可选值: 'text'}",
        "text": "{必填，string}",
        "synthetic": "{选填，boolean}",
        "ignored": "{选填，boolean}",
        "time": {
          "start": "{必填，integer}",
          "end": "{选填，integer}"
        },
        "metadata": {}
      }
    ]
  }
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Send message

**operationId**: `session.prompt`

> Create and send a new message to a session, streaming the AI response.

#### 请求

```
POST {baseUrl}/session/{sessionID}/message?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{选填，string}",
  "model": {
    "providerID": "{必填，string}",
    "modelID": "{必填，string}"
  },
  "agent": "{选填，string}",
  "noReply": "{选填，boolean}",
  "tools": {
    "{键}": "{boolean}"
  },
  "format": {
    "type": "{必填，可选值: 'text'}"
  },
  "system": "{选填，string}",
  "variant": "{选填，string}",
  "parts": [
    {
      "id": "{选填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

#### 响应

**HTTP 200** Created message

```
Content-Type: application/json
```

```json
{
  "info": {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "role": "{必填，可选值: 'assistant'}",
    "time": {
      "created": "{必填，integer}",
      "completed": "{选填，integer}"
    },
    "error": {
      "name": "{必填，可选值: 'ProviderAuthError'}",
      "data": {
        "providerID": "{必填，string}",
        "message": "{必填，string}"
      }
    },
    "parentID": "{必填，string}",
    "modelID": "{必填，string}",
    "providerID": "{必填，string}",
    "mode": "{必填，string}",
    "agent": "{必填，string}",
    "path": {
      "cwd": "{必填，string}",
      "root": "{必填，string}"
    },
    "summary": "{选填，boolean}",
    "cost": "{必填，number}",
    "tokens": {
      "total": "{选填，number}",
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "structured": "{选填，object}",
    "variant": "{选填，string}",
    "finish": "{选填，string}"
  },
  "parts": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "messageID": "{必填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Delete message

**operationId**: `session.deleteMessage`

> Permanently delete a specific message and all of its parts from a session without reverting file changes.

#### 请求

```
DELETE {baseUrl}/session/{sessionID}/message/{messageID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `messageID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully deleted message

```
Content-Type: application/json
```

```json
"{Successfully deleted message，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 409** SessionBusyError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionBusyError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get message

**operationId**: `session.message`

> Retrieve a specific message from a session by its message ID.

#### 请求

```
GET {baseUrl}/session/{sessionID}/message/{messageID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `messageID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Message

```
Content-Type: application/json
```

```json
{
  "info": {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "role": "{必填，可选值: 'user'}",
    "time": {
      "created": "{必填，number}"
    },
    "format": "{选填，OutputFormat 对象}",
    "summary": {
      "title": "{选填，string}",
      "body": "{选填，string}",
      "diffs": [
        "{SnapshotFileDiff 对象}"
      ]
    },
    "agent": "{必填，string}",
    "model": {
      "providerID": "{必填，string}",
      "modelID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "system": "{选填，string}",
    "tools": {
      "{键}": "{boolean}"
    }
  },
  "parts": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "messageID": "{必填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### part.delete

**operationId**: `part.delete`

> Delete a part from a message.

#### 请求

```
DELETE {baseUrl}/session/{sessionID}/message/{messageID}/part/{partID}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `messageID` | path | 是 | string |  |
| `partID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully deleted part

```
Content-Type: application/json
```

```json
"{Successfully deleted part，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### part.update

**operationId**: `part.update`

> Update a part in a message.

#### 请求

```
PATCH {baseUrl}/session/{sessionID}/message/{messageID}/part/{partID}?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `messageID` | path | 是 | string |  |
| `partID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "id": "{必填，string}",
  "sessionID": "{必填，string}",
  "messageID": "{必填，string}",
  "type": "{必填，可选值: 'text'}",
  "text": "{必填，string}",
  "synthetic": "{选填，boolean}",
  "ignored": "{选填，boolean}",
  "time": {
    "start": "{必填，integer}",
    "end": "{选填，integer}"
  },
  "metadata": {}
}
```

#### 响应

**HTTP 200** Successfully updated part

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "sessionID": "{必填，string}",
  "messageID": "{必填，string}",
  "type": "{必填，可选值: 'text'}",
  "text": "{必填，string}",
  "synthetic": "{选填，boolean}",
  "ignored": "{选填，boolean}",
  "time": {
    "start": "{必填，integer}",
    "end": "{选填，integer}"
  },
  "metadata": {}
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Respond to permission

**operationId**: `permission.respond`

> Approve or deny a permission request from the AI assistant.

#### 请求

```
POST {baseUrl}/session/{sessionID}/permissions/{permissionID}?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `permissionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "response": "{必填，可选值: 'once', 'always', 'reject'}"
}
```

#### 响应

**HTTP 200** Permission processed successfully

```
Content-Type: application/json
```

```json
"{Permission processed successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError | PermissionNotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Send async message

**operationId**: `session.prompt_async`

> Create and send a new message to a session asynchronously, starting the session if needed and returning immediately.

#### 请求

```
POST {baseUrl}/session/{sessionID}/prompt_async?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{选填，string}",
  "model": {
    "providerID": "{必填，string}",
    "modelID": "{必填，string}"
  },
  "agent": "{选填，string}",
  "noReply": "{选填，boolean}",
  "tools": {
    "{键}": "{boolean}"
  },
  "format": {
    "type": "{必填，可选值: 'text'}"
  },
  "system": "{选填，string}",
  "variant": "{选填，string}",
  "parts": [
    {
      "id": "{选填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

#### 响应

**HTTP 204** Prompt accepted

（无响应体）

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Revert message

**operationId**: `session.revert`

> Revert a specific message in a session, undoing its effects and restoring the previous state.

#### 请求

```
POST {baseUrl}/session/{sessionID}/revert?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{必填，string}",
  "partID": "{选填，string}"
}
```

#### 响应

**HTTP 200** Updated session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 409** SessionBusyError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionBusyError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Unshare session

**operationId**: `session.unshare`

> Remove the shareable link for a session, making it private again.

#### 请求

```
DELETE {baseUrl}/session/{sessionID}/share?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully unshared session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 500** InternalServerError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InternalServerError'}"
}
```

---

### Share session

**operationId**: `session.share`

> Create a shareable link for a session, allowing others to view the conversation.

#### 请求

```
POST {baseUrl}/session/{sessionID}/share?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Successfully shared session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 500** InternalServerError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InternalServerError'}"
}
```

---

### Run shell command

**operationId**: `session.shell`

> Execute a shell command within the session context and return the AI's response.

#### 请求

```
POST {baseUrl}/session/{sessionID}/shell?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{选填，string}",
  "agent": "{必填，string}",
  "model": {
    "providerID": "{必填，string}",
    "modelID": "{必填，string}"
  },
  "command": "{必填，string}"
}
```

#### 响应

**HTTP 200** Created message

```
Content-Type: application/json
```

```json
{
  "info": {
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "role": "{必填，可选值: 'user'}",
    "time": {
      "created": "{必填，number}"
    },
    "format": "{选填，OutputFormat 对象}",
    "summary": {
      "title": "{选填，string}",
      "body": "{选填，string}",
      "diffs": [
        "{SnapshotFileDiff 对象}"
      ]
    },
    "agent": "{必填，string}",
    "model": {
      "providerID": "{必填，string}",
      "modelID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "system": "{选填，string}",
    "tools": {
      "{键}": "{boolean}"
    }
  },
  "parts": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "messageID": "{必填，string}",
      "type": "{必填，可选值: 'text'}",
      "text": "{必填，string}",
      "synthetic": "{选填，boolean}",
      "ignored": "{选填，boolean}",
      "time": {
        "start": "{必填，integer}",
        "end": "{选填，integer}"
      },
      "metadata": {}
    }
  ]
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 409** SessionBusyError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionBusyError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Summarize session

**operationId**: `session.summarize`

> Generate a concise summary of the session using AI compaction to preserve key information.

#### 请求

```
POST {baseUrl}/session/{sessionID}/summarize?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "providerID": "{必填，string}",
  "modelID": "{必填，string}",
  "auto": "{选填，boolean}"
}
```

#### 响应

**HTTP 200** Summarized session

```
Content-Type: application/json
```

```json
"{Summarized session，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Get session todos

**operationId**: `session.todo`

> Retrieve the todo list associated with a specific session, showing tasks and action items.

#### 请求

```
GET {baseUrl}/session/{sessionID}/todo?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Todo list

```
Content-Type: application/json
```

```json
[
  {
    "content": "{必填，Brief description of the task，string}",
    "status": "{必填，Current status of the task: pending, in_progress, completed, cancelled，string}",
    "priority": "{必填，Priority level of the task: high, medium, low，string}"
  }
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Restore reverted messages

**operationId**: `session.unrevert`

> Restore all previously reverted messages in a session.

#### 请求

```
POST {baseUrl}/session/{sessionID}/unrevert?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Updated session

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "slug": "{必填，string}",
  "projectID": "{必填，string}",
  "workspaceID": "{选填，string}",
  "directory": "{必填，string}",
  "path": "{选填，string}",
  "parentID": "{选填，string}",
  "summary": {
    "additions": "{必填，number}",
    "deletions": "{必填，number}",
    "files": "{必填，number}",
    "diffs": [
      {
        "file": "{选填，string}",
        "patch": "{选填，string}",
        "additions": "{必填，number}",
        "deletions": "{必填，number}",
        "status": "{选填，可选值: 'added', 'deleted', 'modified'}"
      }
    ]
  },
  "cost": "{选填，number}",
  "tokens": {
    "input": "{必填，number}",
    "output": "{必填，number}",
    "reasoning": "{必填，number}",
    "cache": {
      "read": "{必填，number}",
      "write": "{必填，number}"
    }
  },
  "share": {
    "url": "{必填，string}"
  },
  "title": "{必填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "version": "{必填，string}",
  "metadata": {},
  "time": {
    "created": "{必填，integer}",
    "updated": "{必填，integer}",
    "compacting": "{选填，integer}",
    "archived": "{选填，number}"
  },
  "permission": [
    {
      "permission": "{必填，string}",
      "pattern": "{必填，string}",
      "action": "{必填，PermissionAction 对象}"
    }
  ],
  "revert": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 409** SessionBusyError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionBusyError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get session status

**operationId**: `session.status`

> Retrieve the current status of all sessions, including active, idle, and completed states.

#### 请求

```
GET {baseUrl}/session/status?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Get session status

```
Content-Type: application/json
```

```json
{
  "{键}": {
    "type": "{必填，可选值: 'idle'}"
  }
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

## session questions

### List pending question requests

**operationId**: `v2.question.request.list`

> Retrieve pending question requests for a location.

#### 请求

```
GET {baseUrl}/api/question/request?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "questions": [
        "{QuestionV2Info 对象}"
      ],
      "tool": {
        "messageID": "{必填，string}",
        "callID": "{必填，string}"
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### List session question requests

**operationId**: `v2.session.question.list`

> Retrieve pending question requests owned by a session.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/question
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "sessionID": "{必填，string}",
      "questions": [
        "{QuestionV2Info 对象}"
      ],
      "tool": {
        "messageID": "{必填，string}",
        "callID": "{必填，string}"
      }
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Reject pending question request

**operationId**: `v2.session.question.reject`

> Reject a pending question request owned by a session.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/question/{requestID}/reject
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `requestID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError | QuestionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'QuestionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Reply to pending question request

**operationId**: `v2.session.question.reply`

> Answer a pending question request owned by a session.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/question/{requestID}/reply
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `requestID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "answers": [
    [
      "{string}"
    ]
  ]
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError | QuestionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'QuestionNotFoundError'}",
  "requestID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

## sessions

### List sessions

**operationId**: `v2.session.list`

> Retrieve sessions in the requested order. Items keep that order across pages; use cursor.next or cursor.previous to move through the ordered list.

#### 请求

```
GET {baseUrl}/api/session?workspace={workspace}&limit={limit}&order={order}&search={search}&directory={directory}&project={project}&subpath={subpath}&cursor={cursor}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `workspace` | query | 否 | string |  |
| `limit` | query | 否 | number |  |
| `order` | query | 否 | "asc"\|"desc" |  |
| `search` | query | 否 | string |  |
| `directory` | query | 否 | string |  |
| `project` | query | 否 | string |  |
| `subpath` | query | 否 | string |  |
| `cursor` | query | 否 | string |  |

#### 响应

**HTTP 200** SessionsResponse

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "parentID": "{选填，string}",
      "projectID": "{必填，string}",
      "agent": "{选填，string}",
      "model": "{选填，ModelRef 对象}",
      "cost": "{必填，number}",
      "tokens": {
        "input": "{必填，number}",
        "output": "{必填，number}",
        "reasoning": "{必填，number}",
        "cache": {
          "read": "{必填，number}",
          "write": "{必填，number}"
        }
      },
      "time": {
        "created": "{必填，number}",
        "updated": "{必填，number}",
        "archived": "{选填，number}"
      },
      "title": "{必填，string}",
      "location": "{必填，LocationRef 对象}",
      "subpath": "{选填，string}",
      "revert": "{选填，RevertState 对象}"
    }
  ],
  "cursor": {
    "previous": "{选填，string}",
    "next": "{选填，string}"
  }
}
```

**HTTP 400** InvalidCursorError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidCursorError'}",
  "message": "{必填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Create session

**operationId**: `v2.session.create`

> Create a session at the requested location.

#### 请求

```
POST {baseUrl}/api/session
Content-Type: application/json
```

**请求体** (`application/json`):

```json
{
  "id": "{选填，string}",
  "agent": "{选填，string}",
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  },
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}"
  }
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "id": "{必填，string}",
    "parentID": "{选填，string}",
    "projectID": "{必填，string}",
    "agent": "{选填，string}",
    "model": {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "cost": "{必填，number}",
    "tokens": {
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "time": {
      "created": "{必填，number}",
      "updated": "{必填，number}",
      "archived": "{选填，number}"
    },
    "title": "{必填，string}",
    "location": {
      "directory": "{必填，string}",
      "workspaceID": "{选填，string}"
    },
    "subpath": "{选填，string}",
    "revert": {
      "messageID": "{必填，string}",
      "partID": "{选填，string}",
      "snapshot": "{选填，string}",
      "diff": "{选填，string}",
      "files": [
        "{FileDiff 对象}"
      ]
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

### Get session

**operationId**: `v2.session.get`

> Retrieve a session by ID.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "id": "{必填，string}",
    "parentID": "{选填，string}",
    "projectID": "{必填，string}",
    "agent": "{选填，string}",
    "model": {
      "id": "{必填，string}",
      "providerID": "{必填，string}",
      "variant": "{选填，string}"
    },
    "cost": "{必填，number}",
    "tokens": {
      "input": "{必填，number}",
      "output": "{必填，number}",
      "reasoning": "{必填，number}",
      "cache": {
        "read": "{必填，number}",
        "write": "{必填，number}"
      }
    },
    "time": {
      "created": "{必填，number}",
      "updated": "{必填，number}",
      "archived": "{选填，number}"
    },
    "title": "{必填，string}",
    "location": {
      "directory": "{必填，string}",
      "workspaceID": "{选填，string}"
    },
    "subpath": "{选填，string}",
    "revert": {
      "messageID": "{必填，string}",
      "partID": "{选填，string}",
      "snapshot": "{选填，string}",
      "diff": "{选填，string}",
      "files": [
        "{FileDiff 对象}"
      ]
    }
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Switch session agent

**operationId**: `v2.session.switchAgent`

> Switch the agent used by subsequent provider turns.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/agent
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "agent": "{必填，string}"
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Compact session

**operationId**: `v2.session.compact`

> Compact a session conversation.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/compact
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 503** ServiceUnavailableError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ServiceUnavailableError'}",
  "message": "{必填，string}",
  "service": "{选填，string}"
}
```

---

### Get session context

**operationId**: `v2.session.context`

> Retrieve the active context messages for a session (all messages after the last compaction).

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/context
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "metadata": {},
      "time": {
        "created": "{必填，number}"
      },
      "type": "{必填，可选值: 'agent-switched'}",
      "agent": "{必填，string}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 500** UnknownError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnknownError'}",
  "message": "{必填，string}",
  "ref": "{选填，string}"
}
```

---

### Subscribe to session events

**operationId**: `v2.session.events`

> Replay durable events after an aggregate sequence, then continue with new durable events.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/event?after={after}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `after` | query | 否 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: text/event-stream
```

```json
{
  "id": "{必填，string}",
  "event": "{必填，string}",
  "data": "{必填，SessionDurableEventStream 对象}"
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get session history

**operationId**: `v2.session.history`

> Read one finite page of public durable Session events after an exclusive aggregate sequence. Newly committed events may appear on later pages.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/history?limit={limit}&after={after}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `limit` | query | 否 | string |  |
| `after` | query | 否 | string |  |

#### 响应

**HTTP 200** SessionHistory

```
Content-Type: application/json
```

```json
{
  "data": [
    {
      "id": "{必填，string}",
      "metadata": {},
      "type": "{必填，可选值: 'session.next.agent.switched'}",
      "durable": {
        "aggregateID": "{必填，string}",
        "seq": "{必填，integer}",
        "version": "{必填，integer}"
      },
      "location": "{选填，LocationRef 对象}",
      "data": {
        "timestamp": "{必填，number}",
        "sessionID": "{必填，string}",
        "messageID": "{必填，string}",
        "agent": "{必填，string}"
      }
    }
  ],
  "hasMore": "{必填，boolean}"
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Interrupt session execution

**operationId**: `v2.session.interrupt`

> Interrupt active execution owned by this OpenCode process. Idle interruption is a no-op.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/interrupt
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Get session message

**operationId**: `v2.session.message`

> Retrieve one projected message owned by the Session.

#### 请求

```
GET {baseUrl}/api/session/{sessionID}/message/{messageID}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |
| `messageID` | path | 是 | string |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "id": "{必填，string}",
    "metadata": {},
    "time": {
      "created": "{必填，number}"
    },
    "type": "{必填，可选值: 'agent-switched'}",
    "agent": "{必填，string}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError | MessageNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'MessageNotFoundError'}",
  "sessionID": "{必填，string}",
  "messageID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Switch session model

**operationId**: `v2.session.switchModel`

> Switch the model used by subsequent provider turns.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/model
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "model": {
    "id": "{必填，string}",
    "providerID": "{必填，string}",
    "variant": "{选填，string}"
  }
}
```

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Send message

**operationId**: `v2.session.prompt`

> Durably admit one session input and schedule agent-loop execution unless resume is false.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/prompt
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "id": "{选填，string}",
  "prompt": {
    "text": "{必填，string}",
    "files": [
      {
        "uri": "{必填，string}",
        "name": "{选填，string}",
        "description": "{选填，string}",
        "source": "{选填，PromptSource 对象}"
      }
    ],
    "agents": [
      {
        "name": "{必填，string}",
        "source": "{选填，PromptSource 对象}"
      }
    ]
  },
  "delivery": "{选填，可选值: 'steer', 'queue'}",
  "resume": "{选填，boolean}"
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "admittedSeq": "{必填，integer}",
    "id": "{必填，string}",
    "sessionID": "{必填，string}",
    "prompt": {
      "text": "{必填，string}",
      "files": [
        "{PromptFileAttachment 对象}"
      ],
      "agents": [
        "{PromptAgentAttachment 对象}"
      ]
    },
    "delivery": "{必填，可选值: 'steer', 'queue'}",
    "timeCreated": "{必填，number}",
    "promotedSeq": "{选填，integer}"
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 409** ConflictError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ConflictError'}",
  "message": "{必填，string}",
  "resource": "{选填，string}"
}
```

---

### Clear staged revert

**operationId**: `v2.session.revert.clear`

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/revert/clear
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 500** UnknownError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnknownError'}",
  "message": "{必填，string}",
  "ref": "{选填，string}"
}
```

---

### Commit staged revert

**operationId**: `v2.session.revert.commit`

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/revert/commit
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

---

### Stage session revert

**operationId**: `v2.session.revert.stage`

> Stage or move a reversible session boundary and optionally apply its file changes.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/revert/stage
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

**请求体** (`application/json`):

```json
{
  "messageID": "{必填，string}",
  "files": "{选填，boolean}"
}
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {
    "messageID": "{必填，string}",
    "partID": "{选填，string}",
    "snapshot": "{选填，string}",
    "diff": "{选填，string}",
    "files": [
      {
        "path": "{必填，string}",
        "status": "{必填，可选值: 'added', 'modified', 'deleted'}",
        "additions": "{必填，integer}",
        "deletions": "{必填，integer}",
        "patch": "{必填，string}"
      }
    ]
  }
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** MessageNotFoundError | SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'MessageNotFoundError'}",
  "sessionID": "{必填，string}",
  "messageID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 500** UnknownError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnknownError'}",
  "message": "{必填，string}",
  "ref": "{选填，string}"
}
```

---

### Wait for session

**operationId**: `v2.session.wait`

> Wait for a session agent loop to become idle.

#### 请求

```
POST {baseUrl}/api/session/{sessionID}/wait
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `sessionID` | path | 是 | string |  |

#### 响应

**HTTP 204** <No Content>

（无响应体）

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

**HTTP 404** SessionNotFoundError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'SessionNotFoundError'}",
  "sessionID": "{必填，string}",
  "message": "{必填，string}"
}
```

**HTTP 503** ServiceUnavailableError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'ServiceUnavailableError'}",
  "message": "{必填，string}",
  "service": "{选填，string}"
}
```

---

### List active sessions

**operationId**: `v2.session.active`

> Retrieve foreground Session drains currently owned by this OpenCode process. Sessions absent from the result are inactive.

#### 请求

```
GET {baseUrl}/api/session/active
```

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "data": {}
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## skills

### List skills

**operationId**: `v2.skill.list`

> Retrieve currently registered skills.

#### 请求

```
GET {baseUrl}/api/skill?location={location}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `location` | query | 否 | object |  |

#### 响应

**HTTP 200** Success

```
Content-Type: application/json
```

```json
{
  "location": {
    "directory": "{必填，string}",
    "workspaceID": "{选填，string}",
    "project": {
      "id": "{必填，string}",
      "directory": "{必填，string}"
    }
  },
  "data": [
    {
      "name": "{必填，string}",
      "description": "{选填，string}",
      "slash": "{选填，boolean}",
      "location": "{必填，string}",
      "content": "{必填，string}"
    }
  ]
}
```

**HTTP 400** InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'InvalidRequestError'}",
  "message": "{必填，string}",
  "kind": "{选填，string}",
  "field": "{选填，string}"
}
```

**HTTP 401** UnauthorizedError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'UnauthorizedError'}",
  "message": "{必填，string}"
}
```

---

## sync

### List sync events

**operationId**: `sync.history.list`

> List sync events for all aggregates. Keys are aggregate IDs the client already knows about, values are the last known sequence ID. Events with seq > value are returned for those aggregates. Aggregates not listed in the input get their full history.

#### 请求

```
POST {baseUrl}/sync/history?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "{键}": "{integer}"
}
```

#### 响应

**HTTP 200** Sync events

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "aggregate_id": "{必填，string}",
    "seq": "{必填，integer}",
    "type": "{必填，string}",
    "data": {}
  }
]
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Replay sync events

**operationId**: `sync.replay`

> Validate and replay a complete sync event history.

#### 请求

```
POST {baseUrl}/sync/replay?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "directory": "{必填，string}",
  "events": [
    {
      "id": "{必填，string}",
      "aggregateID": "{必填，string}",
      "seq": "{必填，integer}",
      "type": "{必填，string}",
      "data": {}
    }
  ]
}
```

#### 响应

**HTTP 200** Replayed sync events

```
Content-Type: application/json
```

```json
{
  "sessionID": "{必填，string}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Start workspace sync

**operationId**: `sync.start`

> Start sync loops for workspaces in the current project that have active sessions.

#### 请求

```
POST {baseUrl}/sync/start?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Workspace sync started

```
Content-Type: application/json
```

```json
"{Workspace sync started，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Steal session into workspace

**operationId**: `sync.steal`

> Update a session to belong to the current workspace through the sync event system.

#### 请求

```
POST {baseUrl}/sync/steal?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "sessionID": "{必填，string}"
}
```

#### 响应

**HTTP 200** Session stolen into workspace

```
Content-Type: application/json
```

```json
{
  "sessionID": "{必填，string}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

## tui

### Append TUI prompt

**operationId**: `tui.appendPrompt`

> Append prompt to the TUI.

#### 请求

```
POST {baseUrl}/tui/append-prompt?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "text": "{必填，string}"
}
```

#### 响应

**HTTP 200** Prompt processed successfully

```
Content-Type: application/json
```

```json
"{Prompt processed successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Clear TUI prompt

**operationId**: `tui.clearPrompt`

> Clear the prompt.

#### 请求

```
POST {baseUrl}/tui/clear-prompt?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Prompt cleared successfully

```
Content-Type: application/json
```

```json
"{Prompt cleared successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Get next TUI request

**operationId**: `tui.control.next`

> Retrieve the next TUI request from the queue for processing.

#### 请求

```
GET {baseUrl}/tui/control/next?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Next TUI request

```
Content-Type: application/json
```

```json
{
  "path": "{必填，string}",
  "body": "{必填，object}"
}
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Submit TUI response

**operationId**: `tui.control.response`

> Submit a response to the TUI request queue to complete a pending request.

#### 请求

```
POST {baseUrl}/tui/control/response?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
"{object}"
```

#### 响应

**HTTP 200** Response submitted successfully

```
Content-Type: application/json
```

```json
"{Response submitted successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Execute TUI command

**operationId**: `tui.executeCommand`

> Execute a TUI command.

#### 请求

```
POST {baseUrl}/tui/execute-command?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "command": "{必填，string}"
}
```

#### 响应

**HTTP 200** Command executed successfully

```
Content-Type: application/json
```

```json
"{Command executed successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Open help dialog

**operationId**: `tui.openHelp`

> Open the help dialog in the TUI to display user assistance information.

#### 请求

```
POST {baseUrl}/tui/open-help?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Help dialog opened successfully

```
Content-Type: application/json
```

```json
"{Help dialog opened successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Open models dialog

**operationId**: `tui.openModels`

> Open the model dialog.

#### 请求

```
POST {baseUrl}/tui/open-models?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Model dialog opened successfully

```
Content-Type: application/json
```

```json
"{Model dialog opened successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Open sessions dialog

**operationId**: `tui.openSessions`

> Open the session dialog.

#### 请求

```
POST {baseUrl}/tui/open-sessions?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Session dialog opened successfully

```
Content-Type: application/json
```

```json
"{Session dialog opened successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Open themes dialog

**operationId**: `tui.openThemes`

> Open the theme dialog.

#### 请求

```
POST {baseUrl}/tui/open-themes?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Theme dialog opened successfully

```
Content-Type: application/json
```

```json
"{Theme dialog opened successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Publish TUI event

**operationId**: `tui.publish`

> Publish a TUI event.

#### 请求

```
POST {baseUrl}/tui/publish?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "type": "{必填，可选值: 'tui.prompt.append'}",
  "properties": {
    "text": "{必填，string}"
  }
}
```

#### 响应

**HTTP 200** Event published successfully

```
Content-Type: application/json
```

```json
"{Event published successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### Select session

**operationId**: `tui.selectSession`

> Navigate the TUI to display the specified session.

#### 请求

```
POST {baseUrl}/tui/select-session?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "sessionID": "{必填，Session ID to navigate to，string}"
}
```

#### 响应

**HTTP 200** Session selected successfully

```
Content-Type: application/json
```

```json
"{Session selected successfully，boolean}"
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Show TUI toast

**operationId**: `tui.showToast`

> Show a toast notification in the TUI.

#### 请求

```
POST {baseUrl}/tui/show-toast?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "title": "{选填，string}",
  "message": "{必填，string}",
  "variant": "{必填，可选值: 'info', 'success', 'warning', 'error'}",
  "duration": "{选填，integer}"
}
```

#### 响应

**HTTP 200** Toast notification shown successfully

```
Content-Type: application/json
```

```json
"{Toast notification shown successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Submit TUI prompt

**operationId**: `tui.submitPrompt`

> Submit the prompt.

#### 请求

```
POST {baseUrl}/tui/submit-prompt?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Prompt submitted successfully

```
Content-Type: application/json
```

```json
"{Prompt submitted successfully，boolean}"
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

## workspace

### List workspaces

**operationId**: `experimental.workspace.list`

> List all workspaces.

#### 请求

```
GET {baseUrl}/experimental/workspace?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Workspaces

```
Content-Type: application/json
```

```json
[
  {
    "id": "{必填，string}",
    "type": "{必填，string}",
    "name": "{必填，string}",
    "branch": "{选填，string | null}",
    "directory": "{选填，string | null}",
    "extra": "{选填，object | null}",
    "projectID": "{必填，string}",
    "timeUsed": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Create workspace

**operationId**: `experimental.workspace.create`

> Create a workspace for the current project.

#### 请求

```
POST {baseUrl}/experimental/workspace?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "id": "{选填，string}",
  "type": "{必填，string}",
  "branch": "{选填，string | null}",
  "extra": "{选填，object | null}"
}
```

#### 响应

**HTTP 200** Workspace created

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "type": "{必填，string}",
  "name": "{必填，string}",
  "branch": "{选填，string | null}",
  "directory": "{选填，string | null}",
  "extra": "{选填，object | null}",
  "projectID": "{必填，string}",
  "timeUsed": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}"
}
```

**HTTP 400** WorkspaceCreateError | BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorkspaceCreateError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---

### Remove workspace

**operationId**: `experimental.workspace.remove`

> Remove an existing workspace.

#### 请求

```
DELETE {baseUrl}/experimental/workspace/{id}?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `id` | path | 是 | string |  |
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Workspace removed

```
Content-Type: application/json
```

```json
{
  "id": "{必填，string}",
  "type": "{必填，string}",
  "name": "{必填，string}",
  "branch": "{选填，string | null}",
  "directory": "{选填，string | null}",
  "extra": "{选填，object | null}",
  "projectID": "{必填，string}",
  "timeUsed": "{必填，number | \"NaN\" | \"Infinity\" | \"-Infinity\" | \"Infinity\"|\"-Infinity\"|\"NaN\"}"
}
```

**HTTP 400** BadRequest | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "_tag": "{必填，可选值: 'BadRequest'}"
}
```

---

### List workspace adapters

**operationId**: `experimental.workspace.adapter.list`

> List all available workspace adapters for the current project.

#### 请求

```
GET {baseUrl}/experimental/workspace/adapter?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Workspace adapters

```
Content-Type: application/json
```

```json
[
  {
    "type": "{必填，string}",
    "name": "{必填，string}",
    "description": "{必填，string}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Workspace status

**operationId**: `experimental.workspace.status`

> Get connection status for workspaces in the current project.

#### 请求

```
GET {baseUrl}/experimental/workspace/status?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 200** Workspace status

```
Content-Type: application/json
```

```json
[
  {
    "workspaceID": "{必填，string}",
    "status": "{必填，可选值: 'connected', 'connecting', 'disconnected', 'error'}"
  }
]
```

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Sync workspace list

**operationId**: `experimental.workspace.syncList`

> Register missing workspaces returned by workspace adapters.

#### 请求

```
POST {baseUrl}/experimental/workspace/sync-list?directory={directory}&workspace={workspace}
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

#### 响应

**HTTP 204** Workspace list synced

（无响应体）

**HTTP 400** Bad request

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'BadRequest'}",
  "data": {
    "message": "{必填，string}",
    "kind": "{选填，可选值: 'Params', 'Headers', 'Query', 'Body', 'Payload'}"
  }
}
```

---

### Warp session into workspace

**operationId**: `experimental.workspace.warp`

> Move a session's sync history into the target workspace, or detach it to the local project.

#### 请求

```
POST {baseUrl}/experimental/workspace/warp?directory={directory}&workspace={workspace}
Content-Type: application/json
```

**参数说明**:

| 名称 | 位置 | 必填 | 类型 | 说明 |
|---|---|---|---|---|
| `directory` | query | 否 | string |  |
| `workspace` | query | 否 | string |  |

**请求体** (`application/json`):

```json
{
  "id": "{必填，string | null}",
  "sessionID": "{必填，string}",
  "copyChanges": "{选填，boolean}"
}
```

#### 响应

**HTTP 204** Session warped

（无响应体）

**HTTP 400** WorkspaceWarpError | VcsApplyError | InvalidRequestError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'WorkspaceWarpError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

**HTTP 404** NotFoundError

```
Content-Type: application/json
```

```json
{
  "name": "{必填，可选值: 'NotFoundError'}",
  "data": {
    "message": "{必填，string}"
  }
}
```

---


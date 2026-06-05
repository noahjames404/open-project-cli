# op — OpenProject CLI

A fast, colorful Bash CLI for [OpenProject](https://www.openproject.org/). Built because the official CLI lacks assignee display, rich work package details, and quick filtering.

> Requires `bash`, `curl`, and `jq`.

---

## Installation

```bash
# Clone or copy the script somewhere on your PATH
cp op /usr/local/bin/op
chmod +x /usr/local/bin/op
```

---

## Configuration

Create `~/.openprojectrc`:

```bash
OPENPROJECT_URL="https://your-instance.openproject.com"
OPENPROJECT_API_KEY="your-api-key"
```

> Get your API key from **My Account → Access Tokens** in OpenProject.

You can also set these as environment variables — they take precedence over the config file.

The currently selected project is saved in `~/.op-state`.

---

## Commands

### Projects

```bash
op projects list                  # List all projects (active + archived)
op projects list --active         # Active projects only
op projects select <id>           # Select by numeric ID (e.g. 7)
op projects select <identifier>   # Select by slug (e.g. sports360)
op projects select                # Show currently selected project
```

Short alias: `op p`

---

### Work Packages

```bash
op wp list                        # List open work packages (latest activity first)
op wp list -n 10                  # Limit to 10 results
op wp list --all                  # Include closed/done statuses
op wp list --assignee=yanga       # Filter by assignee name (fuzzy match)
op wp list --assignee=4           # Filter by assignee user ID
op wp list --status=developed     # Filter by status name

op wp show <id>                   # Show work package details
op wp show <id> -a                # Show details + full activity log

op wp create -t "Title"                          # Create (defaults to Task type)
op wp create -t "Title" --type=bug               # Create with type
op wp create -t "Title" --type=feature -d "..."  # Create with type + description

op wp edit <id> --title="New title"
op wp edit <id> --status=developed
op wp edit <id> --type=bug
op wp edit <id> --description="Updated details"
op wp edit <id> --assignee=yanga
op wp edit <id> --assignee=4
# All flags can be combined:
op wp edit <id> --title="Fix" --type=bug --assignee=yanga --status="in progress"

op wp comment <id> "Great work, merging soon"    # Add a comment
op wp comment <id> -m "Please review and update status"

op wp help                        # Show full help for wp subcommands
```

Short alias: `op wp`

#### Work Package Output Format

**List view** — card style, two lines per item:
```
  #4386  API: Create profile api for profile image uploads only  3 activities
      Task  ·  Developed  ·  james yanga  ·  2026-06-05 16:13
```

**Show view:**
```
  #4386  |  Task  |  Developed  |  james yanga
  API: Create profile api for profile image uploads only
  ────────────────────────────────────────
  The existing api url `api/player-auth/profile`...
```

**Show with activities (`-a`):**
```
  ────────────────────────────────────────
  4 activities

  2026-06-04 13:48  james yanga
  · Status set to New
  · Assignee set to james yanga

  2026-06-04 16:52  james yanga
  · Status changed from New to Developed
```

---

### Members

```bash
op members list    # List all members of the selected project
op members         # Same as above
```

Short alias: `op m`

---

### General

```bash
op help            # Show top-level help
op version         # Show version
op wp help         # Show work packages help
```

---

## Color Support

Colors are enabled automatically when output is a terminal. To force colors when piping:

```bash
OP_COLOR=1 op wp list | less -R
```

---

## Assignee Name Matching

The `--assignee` flag on `wp list` and `wp edit` accepts:

- **Partial name** — `--assignee=yanga` matches "james yanga"
- **Full name** — `--assignee="james yanga"`
- **User ID** — `--assignee=4` (skips the lookup, fastest)

If multiple members match, the CLI lists them and asks you to be more specific:

```
Multiple users matched 'james':
  27  github james
  4   james yanga
Tip: use --assignee=<user_id> to select exactly.
```

---

## Type & Status Name Matching

`--type` and `--status` flags accept fuzzy names (case-insensitive partial match):

```bash
op wp create -t "Fix crash" --type=bug
op wp edit 42 --status="in progress"
op wp edit 42 --status=dev       # matches "Developed"
```

Available types are fetched from your project's configured types. If no match is found, the CLI lists available options.

---

## Examples

```bash
# Select a project and list recent work
op projects select sports360
op wp list

# Show your own work packages
op wp list --assignee=yanga -n 10

# Create a bug and assign it
op wp create -t "Login fails on Safari" --type=bug --assignee=4

# Update status and leave a comment
op wp edit 4386 --status=developed
op wp comment 4386 "Deployed to staging, ready for QA"

# Review full activity history
op wp show 4386 -a
```

---

## Requirements

| Tool   | Minimum version |
|--------|----------------|
| bash   | 4.0+           |
| curl   | any            |
| jq     | 1.6+           |
| GNU date | (coreutils)  |

> Timestamps are displayed in your system's local timezone (GMT+8 by default on this setup).

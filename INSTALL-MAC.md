# Installing `op` on macOS (Apple Silicon / M-series)

## Prerequisites

You need `bash`, `curl`, and `jq`. Install them via Homebrew if missing:

```bash
# Install Homebrew if you don't have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install jq
brew install jq
```

---

## 1. Get the script

Clone the repo:

```bash
git clone https://github.com/your-org/open-project-cli.git
cd open-project-cli
```

Or if you already have it, just pull the latest:

```bash
git pull
```

---

## 2. Fix line endings (Windows → Unix)

The repo is developed on Windows, so you need to strip `\r` characters before running on Mac:

```bash
# Option A — using dos2unix (install via brew if needed)
brew install dos2unix
dos2unix op

# Option B — using sed (no install needed)
sed -i '' 's/\r//' op
```

> **Why?** Windows saves files with `\r\n` line endings. Mac/Linux expect `\n` only.
> Skipping this step causes errors like `column: illegal option -- N` or `bad substitution`.

---

## 3. Install the script

```bash
chmod +x op

# If Homebrew is installed (recommended for M-series Macs)
cp op /opt/homebrew/bin/op

# If /usr/local/bin exists
cp op /usr/local/bin/op

# Or install to your home bin (no sudo needed)
mkdir -p ~/bin
cp op ~/bin/op
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Verify it's installed:

```bash
which op
op version
```

---

## 4. Configure

Create `~/.openprojectrc` with your OpenProject credentials:

```bash
cat > ~/.openprojectrc << 'EOF'
OPENPROJECT_URL="https://your-instance.openproject.com"
OPENPROJECT_API_KEY="your-api-key"
EOF
```

> Get your API key from **My Account → Access Tokens** in OpenProject.

---

## 5. Test it

```bash
op projects list
op projects select sports360
op wp list
```

---

## Troubleshooting

### `column: illegal option -- N`
You're running an old version. Pull latest and redo step 2:
```bash
git pull
sed -i '' 's/\r//' op
cp op /opt/homebrew/bin/op
```

### `bad substitution` error
Same cause — old version with Windows line endings. Follow the fix above.

### `command not found: op`
The install location isn't on your PATH. Check:
```bash
echo $PATH | tr ':' '\n'
```
Pick a directory from the list and copy `op` there, or add `~/bin` to your PATH (see step 3).

### `jq: command not found`
```bash
brew install jq
```

### `curl: command not found`
curl is pre-installed on macOS. If missing:
```bash
brew install curl
```

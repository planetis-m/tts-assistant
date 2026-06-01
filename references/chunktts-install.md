# chunktts Install Fallback

Use this when `chunktts` is missing or fails to start.

Install only under the user's home directory, never inside the current
workspace.

Supported release assets:
- Linux `x86_64`
- macOS `arm64`
- Windows `x86_64`

Runtime dependencies:
- Linux: install `libcurl` and `libsndfile` through the system package manager.
- macOS: run `brew install curl libsndfile`.
- Windows: keep the DLLs from the release archive beside `chunktts.exe`.

## Linux x86_64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunktts" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunktts/chunktts.tar.gz" \
  "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-linux-x86_64.tar.gz"
rm -rf "$HOME/.local/opt/chunktts/current"
mkdir -p "$HOME/.local/opt/chunktts/current"
tar -xzf "$HOME/.local/opt/chunktts/chunktts.tar.gz" -C "$HOME/.local/opt/chunktts/current"
ln -sfn "$HOME/.local/opt/chunktts/current/chunktts" "$HOME/.local/bin/chunktts"
export PATH="$HOME/.local/bin:$PATH"
chunktts --help >/dev/null
```

## macOS arm64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunktts" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunktts/chunktts.tar.gz" \
  "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-macos-arm64.tar.gz"
rm -rf "$HOME/.local/opt/chunktts/current"
mkdir -p "$HOME/.local/opt/chunktts/current"
tar -xzf "$HOME/.local/opt/chunktts/chunktts.tar.gz" -C "$HOME/.local/opt/chunktts/current"
ln -sfn "$HOME/.local/opt/chunktts/current/chunktts" "$HOME/.local/bin/chunktts"
export PATH="$HOME/.local/bin:$PATH"
chunktts --help >/dev/null
```

## Windows x86_64 (PowerShell)

```powershell
$ErrorActionPreference = "Stop"
$dst = "$HOME\.local\opt\chunktts"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
$zip = Join-Path $env:TEMP "chunktts.zip"
Invoke-WebRequest -Uri "https://github.com/planetis-m/chunktts/releases/latest/download/chunktts-windows-x86_64.zip" -OutFile $zip
Remove-Item -Recurse -Force (Join-Path $dst "current") -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force -Path (Join-Path $dst "current") | Out-Null
Expand-Archive -Path $zip -DestinationPath (Join-Path $dst "current") -Force
$exe = Get-ChildItem -Path (Join-Path $dst "current") -Recurse -Filter "chunktts.exe" | Select-Object -First 1
$exeDir = Split-Path -Parent $exe.FullName
$env:Path = "$exeDir;$env:Path"
chunktts --help | Out-Null
```

## API Key Setup

After installation, tell the user to configure a DeepInfra API key before
generating audio.

Recommended environment variable:
- Linux/macOS: `export DEEPINFRA_API_KEY="your_api_key"`
- Windows PowerShell: `$env:DEEPINFRA_API_KEY = "your_api_key"`

Alternatively, create `config.json` beside the real binary, for example in
`~/.local/opt/chunktts/current/`:

```json
{
  "api_key": "your_deepinfra_api_key"
}
```

## Notes

- Keep the extracted runtime files with the real binary. Do not copy only
  `chunktts` or `chunktts.exe` elsewhere.
- If installation fails because of permission or sandbox restrictions, request
  escalated permission and retry.
- If platform/architecture is unsupported, stop and ask the user for manual installation steps.

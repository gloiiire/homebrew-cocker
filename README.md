# homebrew-cocker

Homebrew tap for [**cocker**](https://github.com/gloiiire/cocker) — a Docker-compatible container engine for Apple Silicon, powered by Apple's `Virtualization.framework`.

## Install

```bash
brew tap gloiiire/cocker
brew install cocker
```

## Prerequisites

cocker needs three things before it can launch containers:

### 1. An Apple Development signing certificate (free)

Required because `cockerd` uses `Virtualization.framework`. macOS only grants the `com.apple.security.virtualization` entitlement to binaries signed by a real Apple developer certificate — not an ad-hoc signature.

If you don't already have one (Xcode users usually do):

1. Open **Xcode → Settings → Accounts**
2. Sign in with your Apple ID (a free account is enough)
3. Click **Manage Certificates → "+" → Apple Development**

The formula's `post_install` step auto-detects your certificate and signs `cockerd` with it. If no cert is found at install time, install the cert and re-run:

```bash
brew postinstall cocker
```

### 2. Apple's container Linux kernel

cocker boots a Linux VM per container, using the same kernel ship with Apple's `container` CLI:

```bash
brew install container
```

If you install it after cocker, finish setup with `brew postinstall cocker`.

### 3. Start the daemon

```bash
brew services start cocker
```

Logs live at `$(brew --prefix)/var/log/cockerd.log`.

## Verify

```bash
cocker version
cocker pull alpine:latest
cocker run -d alpine:latest -- /bin/sh -c 'while true; do date; sleep 1; done'
cocker ps
```

## Upgrade

```bash
brew update
brew upgrade cocker
```

Your container state in `~/.cocker` is preserved across upgrades.

## Uninstall

```bash
brew services stop cocker
brew uninstall cocker
rm -rf ~/.cocker  # only if you want to wipe all container state
```

## Why a separate tap?

cocker depends on Apple-specific entitlements and per-user code signing, which don't fit the assumptions of `homebrew/core` (which expects bottles distributable to any machine). Shipping via a tap with a build-from-source formula lets each user sign with their own developer certificate at install time.

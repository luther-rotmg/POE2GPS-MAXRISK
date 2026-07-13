<div align="center">

# ⚡ POE2GPS-MAXRISK
### The wild sibling of POE2GPS. Everything the safe overlay refused to ship.

*Auto-flask. Reactive dodge. Buff maintenance. Skill rotation. Panic escape. League-mechanic auto-triggers. A comprehensive suite that **acts** on your character.*

[![Latest release](https://img.shields.io/github/v/release/luther-rotmg/POE2GPS-MAXRISK?sort=semver&label=release)](https://github.com/luther-rotmg/POE2GPS-MAXRISK/releases)
![Supports PoE2 through patch 0.5.4](https://img.shields.io/badge/supports%20PoE2-through%20patch%200.5.4-2ea043)
![.NET 10](https://img.shields.io/badge/.NET-10-512BD4?logo=dotnet)
![Windows x64](https://img.shields.io/badge/Windows-x64-0078D6)
![License: proprietary](https://img.shields.io/badge/license-proprietary-red)
[![Discord](https://img.shields.io/badge/Discord-join%20us-5865F2?logo=discord&logoColor=white)](https://discord.gg/32qdzWRja3)
[![Support on Ko-fi](https://img.shields.io/badge/Ko--fi-support%20the%20dev-FF5E5B?logo=kofi&logoColor=white)](https://ko-fi.com/lutherrotmg)
<br>
![Input: writes to game](https://img.shields.io/badge/input-writes%20to%20game-red)
![Risk: high](https://img.shields.io/badge/detection%20risk-HIGH-red)
![Main account: DO NOT USE](https://img.shields.io/badge/main%20account-DO%20NOT%20USE-red)
![Obfuscation: ConfuserEx v2](https://img.shields.io/badge/obfuscation-ConfuserEx%20v2-orange)

</div>

---

## ☠️ Read this first — the risk warning

**POE2GPS-MAXRISK is a high-risk power-user tool. It does things the game's operator (GGG) explicitly prohibits.** It fires flasks for you. It presses skill keys. It clicks league altars. It portals you out on HP crash. It moves the mouse. It sends keyboard input to Path of Exile 2.

Unlike its cousin [POE2GPS](https://github.com/luther-rotmg/POE2GPS) — which is strictly read-only and community-safe — **MAXRISK is not safe** for accounts you care about. Ban risk is real, meaningful, and non-zero. GGG's stance on tools that send input to the game is unambiguous: they don't allow it. If they catch you, they will take action. That action could be a warning, a temporary suspension, or a permanent ban — including of MTX purchases, ladder standings, and league progress.

> ### 🚨 If you value an account, do not use this tool on it.
>
> I don't use MAXRISK on my own main account. Neither should you.

### If you use it anyway — the least-bad path

I haven't fully mapped what triggers a ban and what doesn't. GGG's detection surface is not documented. But if you're going to run MAXRISK, do this:

- **Use a throwaway account.** A brand-new account with nothing on it. No MTX. No stash tabs. No league progress you'd miss.
- **Different machine if you can swing it.** Not your main gaming PC. A cheap laptop or a spare desktop is safer than a VM (VMs have their own detection signals).
- **Different network.** Different IP address than your main account has ever used. Public Wi-Fi, a hotspot, a friend's connection, a VPN endpoint your main hasn't touched. Never link the throwaway to your main by network.
- **Different browser + email for account signup.** No shared cookies, no shared password manager entries.
- **Never log into your main from the MAXRISK machine.** Ever. This is the fastest way to link the two.

None of this makes MAXRISK safe. It just makes the throwaway account the one that eats the risk instead of your main.

### 🔐 Additional hardening — the limited-user Windows setup

The strongest technical hardening you can layer on top of the throwaway-account discipline: run **PoE2 under a separate, limited (Standard) Windows user** that is **denied all access to the MAXRISK folder**. The game — and anything running inside it — then *literally cannot read MAXRISK's files or memory*, while MAXRISK (running as your normal admin account) can still read the game. Pure Windows account isolation; the tool touches nothing in the game to make this work.

> **Why it matters:** a process can only read what its user account is allowed to. The game runs as a low-privilege account that's (a) blocked from the MAXRISK folder by an explicit Deny, and (b) too low-privilege to read your admin-level overlay's memory. Your overlay runs as *you* (admin), which can still read the game. A one-way mirror.

**One-time setup** — open **Command Prompt as Administrator**, then:

**1. Create a limited user for the game** (it's a Standard, non-admin user by default — choose any password, you'll use it to launch):
```bat
net user PoEPlayer * /add
```
The `*` makes it prompt for a password (typed hidden, twice). *GUI alternative: Settings → Accounts → Other users → Add account → "I don't have this person's sign-in info" → "Add a user without a Microsoft account".*

**2. Deny that user every permission on the MAXRISK folder** (use your real unzip path):
```bat
icacls "C:\Games\POE2GPS-MAXRISK" /deny "PoEPlayer:(OI)(CI)F"
```
`(OI)(CI)` applies it to all files + subfolders, `F` is Full control, and `/deny` is an **explicit block that overrides any inherited "allow."** Verify it stuck: run `icacls "C:\Games\POE2GPS-MAXRISK"` and look for a `(DENY)` line for `PoEPlayer`.

**3. Launch PoE2 as that limited user:**
- **Standalone client** — make a shortcut whose **Target** is (the full `runas.exe` path is the reliable form inside a shortcut):
  ```
  C:\Windows\System32\runas.exe /user:PoEPlayer /savecred "C:\Path\To\PathOfExile.exe"
  ```
  `/savecred` caches the password after the **first** launch so it won't ask again. Prefer to type it every time? Just drop `/savecred`.
- **Steam** — Steam runs only **one instance per PC**, so you must **fully exit your own Steam first** (right-click the tray icon → **Exit**), then start it as the limited user with a shortcut. Set the shortcut **Target** to the line matching your Steam drive:
  ```
  C:\Windows\System32\runas.exe /user:PoEPlayer /savecred "C:\Program Files (x86)\Steam\steam.exe"
  ```
  ```
  C:\Windows\System32\runas.exe /user:PoEPlayer /savecred "D:\Steam\steam.exe"
  ```
  The **first** run does two one-time things: Windows asks for `PoEPlayer`'s password (`/savecred` remembers it afterward), and **Steam makes you log in again** — it's a brand-new Steam profile under the limited user (your Steam password + Steam Guard; tick "remember me" so it sticks). After that it's **one click**: the shortcut opens Steam as `PoEPlayer`, and you launch PoE2 from there. *(Heads-up: only the MAXRISK folder gets the Deny — `PoEPlayer` still needs to read the Steam/game files, which it can by default. If Steam sits in `Program Files` and won't update as a standard user, install it to a plain folder like `D:\Steam`, or grant `PoEPlayer` **Modify** on the Steam folder. The standalone client avoids all of this.)*

**4. Run MAXRISK as your normal account** — `Overlay.exe` **as Administrator**, exactly as [Download](#-download) says. Admin reads the game fine; the game can't read back.

Pair the limited-user setup with the throwaway-account discipline above and you're doing every hardening layer available to a user. It **does not** make MAXRISK safe — nothing does — but it closes off one of the most common detection paths (the game process reading the tool's on-disk files or in-memory state). *(Adapted from the community [Run PoE as Limited User](https://www.ownedcore.com/forums/mmo/path-of-exile/poe-bots-programs/676345-run-poe-limited-user.html) guide, syntax-verified against Windows 11.)*

**If in doubt — use [POE2GPS](https://github.com/luther-rotmg/POE2GPS) instead.** It's the same author, same brand, same care, and it will never risk your account.

---

## ⚡ What MAXRISK does — 18 shipped modules

MAXRISK ships 18 shipped modules across four families. Everything POE2GPS reads and draws, MAXRISK also does — plus it *acts* on the game.

### 🎯 Combat auto-play

Skill automation and safety net modules that fire in combat or emergency situations.
- **Auto-Flask** — fires flasks with cooldown discipline; F8 kill-switch
- **Panic Cascade** — HP-crash portal-out + kill-switch
- **Buff Maintenance** — keeps configured buffs applied; foreground-gated
- **Reactive Dodge** — dodge roll on incoming projectile; opt-in
- **Freeze/Shock Awareness** — flask trigger on freeze/shock elemental threat
- **Hotkey Remap** — module toggle + panic hotkey glue

### 💰 Loot & economy

Modules that assist with loot identification and economic decisions.
- **Loot Highlight** — highlights rare/notable drops on the overlay
- **Ninja Pricer** — poe.ninja live-price overlay for items under cursor
- **DPS Calc** — real-time DPS estimator for the equipped skill
- **Post-Combat Drop Overlay** — combat-quiet summary of the last fight's chaos/divine/rare drops; Dashboard-SSE state only in v0.4.0-alpha, pixel drawing v0.4.1

### 🏗️ Build & QoL

Modules that help with character development and quality of life.
- **Build Helper** — resistance analyzer + top-3 build recommendations
- **Stash Sort** — auto-organizes stash items; foreground-gated
- **Enemy Status** — freeze/shock/ignite indicator over enemies

### ⏱️ Meta & session

Modules that track session metrics and provide meta information.
- **Dashboard (M0)** — localhost:7778 SPA; loopback-only; SSE-driven live state
- **AFK Gate** — input suppression when player is idle
- **XP Tracker** — session XP + XP/hr
- **Session Pipeline** — captures per-map performance metrics
- **League Awareness** — ritual/breach/delve state tracking + heartbeat

---

## 🛡️ What MAXRISK will never do

Even the wild sibling has hard lines:

| ❌ Never | Why |
|---|---|
| Automate the full campaign | If you want a campaign runner, that's a different, paid product. MAXRISK is a session assistant. |
| Pick your build for you | You build the character. MAXRISK just makes flask / dodge / buff faster than your fingers. |
| Allocate passives or socket gems for you | Same reason. Character progression is your responsibility. |
| AFK-farm on your behalf | The AFK-detect module *hard-pauses* everything when you're not at the keyboard. This is a safety feature, not a bug. |
| Inject into the game process | No DLL injection. No code executing inside PoE2's memory space. Read-only memory inspection only. |
| Manipulate packets | No traffic interception, no server-side tampering. |
| Try to hide from GGG's detection | MAXRISK is not undetectable and does not claim to be. That's why it's called MAXRISK. |

---

## 🚀 Download

**v0.4.0** is available on the [Releases tab](https://github.com/luther-rotmg/POE2GPS-MAXRISK/releases). Download the latest zip, verify the SHA256 sidecar, unzip, run `Overlay.exe` from a limited-user Windows account (see risk section above). See the [private repo README](https://github.com/luther-rotmg/POE2GPS-MAXRISK-src/blob/master/README.md#-release-workflow-v040) for what's inside the zip and how the release is built.

If v0.4.0 hasn't landed at the Releases tab yet, MAXRISK is between an alpha tag and the public release — check the [Discord](https://discord.gg/32qdzWRja3) `#maxrisk-releases` channel for progress.

---

## 🧪 Testers program

MAXRISK is in **v0.4.0-alpha** private testing. Volunteer testers get early access to the obfuscated binary through Discord.

- **What ships in v0.4.0-alpha:** All 18 modules from the inventory above, Dashboard SPA on localhost:7778, mock-endpoint injection for headless testing (`MAXRISK_MOCK_*` env vars), the full compliance-gate v2 moat closure. Overlay pixel drawing for M19 (Post-Combat Drop Overlay) is deferred to v0.4.1 — v0.4.0-alpha delivers the SSE-only view.
- **How to join:** come to the [Discord](https://discord.gg/32qdzWRja3) and ping in `#maxrisk-testers`. Testers get access to the private repo's `TESTING.md` for the 4 tester-verifier vectors per module.
- **What's next:** the v0.4.0 public release ceremony is Sub-project C of the "get out 0.4.0" arc; overlay pixel drawing lands in v0.4.1.

---

## 🎮 Community

- 💬 **Discord**: [join us](https://discord.gg/32qdzWRja3) — this is where support happens, where issues get triaged, and where I post release notes. If you're using MAXRISK, be here.
- 🐛 **Issues**: use the [Issues](https://github.com/luther-rotmg/POE2GPS-MAXRISK/issues) tab. Include the version, the module that misbehaved, and — if you're comfortable — steps to reproduce.
- ⭐ **Enjoying MAXRISK? Star the repo.** Free tools live and die on visibility.

---

## ❓ FAQ

**Q: Is this the same author as POE2GPS?**
Yes. Same author, same brand DNA, same care. POE2GPS is the safe read-only version; MAXRISK is the wild version. Both by [luther-rotmg](https://github.com/luther-rotmg).

**Q: Why is MAXRISK closed source?**
Public source on this kind of tool is a legal and reputational liability I'm not willing to take on. If a paid tier ever happens, licensing keys enforce it; today the free binary is the only thing on the table.

**Q: Will there be a Linux / macOS build?**
No. PoE2 is Windows only in practice; the tool follows.

**Q: What's the update model?**
Tagged releases on this repo's Releases tab. There is no auto-update in the current build — you re-download when a new version drops. If the game patches and MAXRISK's memory reads break, expect a release within days.

**Q: Can I contribute code?**
Not to this repo. This repo hosts the compiled binaries. If you want to help the ecosystem, contribute to [POE2GPS](https://github.com/luther-rotmg/POE2GPS) (public MIT) — its improvements ripple downstream.

**Q: What if my account gets banned?**
I'm sorry. You were warned. Please don't use MAXRISK on your main.

**Q: Is there a way to make it safer?**
See "the least-bad path" in the risk section above. Beyond that: use POE2GPS instead.

**Q: Is v0.4.0 obfuscated?**
Yes. Every shipped binary from v0.4.0 forward is processed through ConfuserEx v2 (mkaring fork) with the full protection stack: rename, control-flow flattening, string encryption, anti-tamper, anti-debug, invalid-metadata. See the [private repo](https://github.com/luther-rotmg/POE2GPS-MAXRISK-src) for the release harness.

**Q: Where are the screenshots?**
Coming in v0.4.1. Sub-project B (this README refresh) shipped without them because the obfuscated build hadn't hit testers yet; real screenshots ship after the release ceremony.

---

## 📜 License

Proprietary. See [LICENSE](LICENSE). Personal, non-commercial use only. No redistribution of modified binaries. No reverse-engineering.

---

<div align="center">

*MAXRISK is the sibling of [POE2GPS](https://github.com/luther-rotmg/POE2GPS). Same author. Same brand. Same craft. Very different risk profile.*

**⭐ Star the repo if you find this useful — it's the single biggest thing that keeps the project visible.**

</div>
# Microsoft Management Console

A Windows tool for grouping **snap-ins** (administrative tools) to manage hardware, software, and network components — locally or remotely.

---

## Key Facts

| Item | Detail |
| --- | --- |
| **Purpose** | Group snap-ins into a single customized console |
| **Availability** | Since Windows Server 2000; runs on all Windows versions |
| **Launch** | Type `mmc` in Start menu |
| **Default state** | Blank console on first open |
| **Saved as** | `.msc` file (Microsoft Saved Console) |
| **Default save location** | Windows Administrative Tools directory (Start menu) |
| **Scope** | Can manage **local** or **remote** systems |

---

## What MMC Can Do

- Group multiple administrative tools into **one console**.
- Manage **hardware, software, and network components**.
- Create **custom tools** and distribute them to users.
- Add snap-ins for **local or remote** computer management.
- Save custom consoles as `.msc` files for reuse.

---

## Snap-ins

- **Snap-ins** = individual administrative tools added to MMC.
- Each snap-in can target **local computer** or **another computer on the network**.
- Examples: Services, Event Viewer, Device Manager, Disk Management, Group Policy, etc.
- Multiple snap-ins can be combined into a single console.

> MMC is essentially a **container** — the snap-ins do the actual work.
> 

---

## Workflow — Creating a Custom Console

```
1. Open MMC          →  Type "mmc" in Start menu
2. Add snap-ins      →  File → Add or Remove Snap-ins
3. Choose target     →  Local computer OR remote computer
4. Repeat            →  Add more snap-ins as needed
5. Save console      →  File → Save As → .msc file
6. Reuse             →  Load saved .msc anytime
```

---

## Navigation Path

```
File → Add or Remove Snap-ins → Select snap-in → Choose local/remote → Add → OK
```

---

## Saving & Loading Consoles

```
# Save custom console
File → Save As → <name>.msc
Default location: Windows Administrative Tools (Start menu)

# Load saved console
Open MMC → File → Open → select .msc file
OR
Start menu → Windows Administrative Tools → <name>.msc
```

---

## Common Snap-ins (Examples)

| Snap-in | Purpose |
| --- | --- |
| **Services** | Start/stop/configure Windows services |
| **Event Viewer** | View system/app/security logs |
| **Device Manager** | Manage hardware devices |
| **Disk Management** | Manage disks and partitions |
| **Group Policy** | Configure local/domain policies |
| **Computer Management** | Combined console (multiple tools) |
| **Performance Monitor** | Monitor system performance |

> Many standalone admin tools (e.g., `services.msc`, `eventvwr.msc`, `devmgmt.msc`) are just **pre-built MMC consoles**.
> 

---

## Pentesting Uses

| Use Case | Technique |
| --- | --- |
| Enumerate services | `services.msc` (or via MMC snap-in) |
| Check event logs | `eventvwr.msc` — hunt for suspicious activity |
| Review local policy | `gpedit.msc` / Group Policy snap-in |
| Remote management | Add snap-in → target remote computer |
| Persistence discovery | Review scheduled tasks, services, startup items |
| Credential hunting | Check saved consoles / admin tools for cached info |

---

## Key Takeaways

- **MMC** = container for **snap-ins**; blank by default until you add tools.
- **Snap-ins** = the actual administrative tools (Services, Event Viewer, etc.).
- Can target **local or remote** computers — useful for admin and lateral movement.
- Custom consoles saved as **`.msc`** files for reuse and distribution.
- Many familiar Windows tools (`services.msc`, `eventvwr.msc`) are **pre-built MMC consoles**.
- Launch anytime by typing **`mmc`** in the Start menu.
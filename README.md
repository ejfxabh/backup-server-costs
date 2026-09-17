# backup servers: how to choose offsite backup server hosting without overpaying, with real plan prices and bandwidth costs

Search "backup servers" and you'll get two very different crowds. One group wants to know how to back up the servers they already run. The other group is shopping for a server whose whole job is to receive backups — a cheap box in a distant data center that holds the offsite copy of everything. This article covers both angles, but it leans toward the second one, because that's where the money decisions live: what hardware you rent, what storage tier you pick, and — the part that quietly ruins budgets — what it costs to get your data back out.

The short version: for most people and small teams, a cloud VM with big, slow HDD storage and free inbound traffic is the sweet spot for a backup target. Below I'll walk through why, what specs actually matter, and what a provider like Sharktech (a long-running US-based host with DDoS-protected cloud and bare-metal services) currently charges for exactly that kind of setup, with live plan prices verified from their order pages.

## What people actually mean when they search "backup servers"

Both interpretations are legitimate, and they lead to different purchases:

- **"I need to back up my servers."** You're shopping for strategy and software — snapshots, image exports, tools like restic, Borg, or Proxmox Backup Server, and somewhere to send the results.
- **"I need a server for backups."** You're shopping for infrastructure — storage capacity, a network link that doesn't punish you for uploading, root access so your backup tool can run, and enough distance from your primary site that one disaster doesn't eat both copies.

If you're in the first group, the second group's purchase is still your destination. Every backup strategy ends with the same question: where does the offsite copy live? So let's start with the rule that forces that question.

## The 3-2-1 rule, and why the offsite copy is the expensive part

The classic 3-2-1 backup rule says: keep three copies of your data, on two different media types, with one copy offsite. Most people manage the first two points with a NAS and an external drive. The offsite copy is where things fall apart, usually for one of three reasons:

- Uploading to consumer cloud storage gets slow and expensive as you pass a few terabytes.
- Keeping a second physical drive at a friend's house works until that friend moves, or until you realize nobody ever actually swaps the drives.
- Renting generic cloud object storage from a hyperscaler works great until the first time you need to restore 2TB and discover what egress costs.

That last point deserves a number. Major hyperscalers commonly charge around $0.09 per GB for outbound transfer — roughly $90 per terabyte. Restore 5TB in an emergency and the bandwidth line item can exceed the cost of the server you were protecting. Any backup server discussion that skips egress pricing isn't a complete one.

## What a good backup server actually needs

Before looking at any specific host, here's the checklist worth holding providers against:

**Storage type: HDD is fine, honestly.** Backups are written once (or incrementally) and read rarely. You don't need NVMe speeds for a target that mostly absorbs rsync traffic at whatever pace your upstream connection allows. HDD storage is typically the cheapest tier by a wide margin, and sequential throughput is more than enough for restore jobs.

**Free or cheap inbound traffic.** Your backups flow *into* this server constantly. Ingress fees are pure poison for a backup target.

**Predictable, low egress.** The day you restore everything is the day this matters. Look for included transfer measured in terabytes and overage priced per GB in fractions of a cent, not dimes.

**Root access and OS control.** Your backup software — restic, Borg, Duplicity, Proxmox Backup Server, whatever — needs to run on the box as a normal Linux workload. Locked-down backup appliances only work if they speak your exact protocol.

**Geographic separation.** The offsite copy should be in a different building, ideally a different city, than your primary infrastructure. A provider with multiple data center locations lets you pick.

**A way out.** If the provider makes it hard to download your data or export disk images, that's not a backup, that's a hostage situation.

## Your three main options, compared

| Option | Typical cost shape | Best for | Weak spot |
| --- | --- | --- | --- |
| Managed cloud backup apps (Backblaze-style) | Flat per-GB monthly fee | Laptops, desktops, small file sets | Agent-based, limited server features, restore speed capped |
| Cloud VM with big storage (storage VPS / cloud instance) | $10–$80/mo for 1–5TB class | Servers, VMs, homelabs — most flexible | You manage the backup software yourself |
| Dedicated storage server (bare-metal) | $100–$300+/mo | Multi-terabyte archives, 10TB+ | Overkill and overpriced below ~5TB |

The middle row is where most readers of a "backup servers" search belong, and it's the row where provider choice matters most. That's the frame for looking at Sharktech's cloud lineup.

## Running your backup target on a Sharktech cloud VM

Sharktech has been around since 2003, runs DDoS-protected infrastructure in five locations (Los Angeles, Las Vegas, Denver, Chicago, Amsterdam), and their cloud platform is OpenStack-based with a Virtuozzo management panel. For a backup server specifically, four things stand out from their current offering:

**A genuinely cheap HDD tier.** Their cloud storage is split into NVMe, SSD, and HDD, and the published pay-as-you-go rate for HDD is $0.00002 per GB per hour. Do the math at 720 hours a month: that's about $0.0144 per GB per month, or roughly **$14.40 per TB** on top of a plan. Two extra terabytes of backup storage lands around $29/month. Their own FAQ explicitly calls HDD "suitable for large archives and backup storage," so this isn't a hack — it's the intended use.

**Inbound traffic is free and unlimited.** Every backup job you push is ingress. It costs nothing, which is exactly what you want from a target.

**Outbound is included then metered cheaply.** Plan pages list 20TB of included bandwidth, and additional outbound transfer is billed at $0.002 per GB — about **$2 per terabyte**. The cloud info page also notes unlimited incoming with extra outgoing at that same $0.002/GB rate. Even a worst-case full restore of several terabytes costs less than a dinner.

**No lock-in on your data.** You can download VM disk images at any time, via the portal or API — their own marketing pitches this specifically for offsite backup and disaster recovery. You can also upload your own images and ISOs. For a backup infrastructure, being able to walk away with everything intact is a feature worth more than it first appears.

Also included at no extra charge: DDoS protection on the network, a 99.999% uptime guarantee on the cloud platform, snapshot scheduling, backup plans and recovery points in the management panel, and full OpenStack API access for scripted operations. And if you'd rather not run your own backup tooling at all, their order flow has offered an optional Acronis cloud backup add-on at $4/month, as noted in HostAdvice's hands-on review of the platform.

If that sounds like your kind of setup, 👉 check Sharktech's current cloud plans and run your numbers through their cost calculator before committing.

## All current Public Cloud plans and prices

Here is the full Public Cloud lineup as currently shown on their order page, including the configurable ranges per tier. Prices are USD, monthly, and "starting from" because every tier can be scaled up within (or beyond) its range under pay-as-you-go billing.

| Plan | vCPU (included–max) | RAM (included–max) | Storage ranges (SSD / HDD / NVMe) | Bandwidth | Price (from, monthly) | Order |
| --- | --- | --- | --- | --- | --- | --- |
| Small | 4–16 | 8–32 GB | 300–2400 GB SSD / up to 4800 GB HDD / up to 1200 GB NVMe | 20TB+ | $39.00 | [ Order Small](https://bit.ly/SharKTech) |
| Medium | 8–32 | 16–64 GB | 800–6400 GB SSD / up to 12800 GB HDD / up to 3200 GB NVMe | 20TB+ | $79.00 | [ Order Medium](https://bit.ly/SharKTech) |
| Large | 32–128 | 64–256 GB | 1500–12000 GB SSD / up to 24000 GB HDD / up to 6000 GB NVMe | 20TB+ | $249.00 | [ Order Large](https://bit.ly/SharKTech) |
| Enterprise | 64+ (uncapped) | 128+ GB (uncapped) | 5000+ GB SSD / uncapped HDD and NVMe | 20TB+ | $499.00 | [ Order Enterprise](https://bit.ly/SharKTech) |
| Custom | Built to spec | Built to spec | Any mix, negotiated | Negotiated | Quote | [ Contact sales for a Custom plan](https://bit.ly/SharKTech) |

A few notes that make this table make sense:

- **Pay-as-you-go overage rates** (billed hourly beyond included resources): CPU $0.0025/hr per core, RAM $0.0035/hr per GB, SSD $0.00006/hr per GB, HDD $0.00002/hr per GB, NVMe $0.00009/hr per GB.
- **IP addresses:** you get one public IPv4 free at activation; extras are $1.50/month each.
- **Public Cloud vs Dedicated Cloud:** the same platform comes in a second billing flavor called Dedicated Cloud, sold in fixed monthly sizes (Tiny through Colossal) where you prepay an exact resource allocation and the bill never moves. Public Cloud is the better fit for backup targets because storage spikes get absorbed by PAYG instead of forcing a plan migration. Non-Enterprise Public Cloud plans do have a maximum resource cap, which is actually a safety feature — your bill can't spiral if a runaway process eats resources.

**A worked example, using their published rates:** take the Small plan at $39/month (4 cores, 8GB RAM, 300GB SSD), then add 2TB of HDD for your archive. At $0.00002/GB/hr, that's about $28.80/month more. Total: roughly **$68/month for a backup node with 2.3TB of storage**, 20TB of included transfer, DDoS-protected network, and a static IP. Compare that to what 2.3TB costs in hyperscaler block storage plus egress insurance, and the gap is not subtle.

For sizing intuition: Small comfortably handles a homelab or a couple of business servers; Medium suits a stack of VMs or a small fleet; Large and Enterprise exist for agencies and MSPs consolidating client backups. 👉 see today's public cloud pricing and calculator to pressure-test your own configuration.

## What restores actually cost — the number everyone forgets

Restoring is the egress event. Using their published $0.002/GB overage rate beyond included transfer:

- Restore 1TB beyond your allowance: about $2
- Restore 5TB beyond your allowance: about $10
- Restore 20TB beyond your allowance: about $40

Compare that to roughly $90/TB at hyperscaler-standard egress pricing, where that same 20TB restore would run around $1,800. When people say "backup servers are expensive," they're usually describing the wrong provider, not the task. The cheapest backup server is one where the recovery doesn't require a budget meeting.

## When a dedicated server makes more sense

Once your archive passes roughly 5–10TB, or you want the disks to yourself for I/O-heavy workloads (think Proxmox Backup Server with deduplication on fast storage, or monthly full-image dumps), bare-metal starts competing on price. Sharktech's dedicated bare-metal servers are listed from **$219/month**, fully customizable, with 1Gbps to 40Gbps network options, DDoS protection, a hardware management panel, and a 99.99% uptime guarantee.

One concrete data point from a recent official promo: a bare-metal config with 128GB RAM, 2TB NVMe, and 300TB of egress on a 10G port, flat-fee at **$259/month**. That's a lot of machine for the money — though for a pure backup target it's more power than the job needs.

Two caveats from their own dedicated servers page: due to hardware shortages they can't guarantee sub-24-hour delivery on customized bare-metal, and if a specific configuration isn't listed, it goes through their sales team (who, per the page, respond within hours). 👉 check current dedicated server availability and pricing if you're sizing an archive in double-digit terabytes.

## Things worth knowing before you pay

**No money-back guarantee.** Per HostAdvice's review, all payments are non-refundable, including setup fees; the only recourse is a billing dispute within 30 days, which — if upheld — results in account credit, not a refund. Test small before scaling. The hourly PAYG model does let you experiment for cents, so there's little excuse for committing blind.

**Payment options are unusually broad:** credit cards, PayPal, wire transfers, Western Union, and Alipay — handy for international buyers.

**Reviews are decent but not unanimous.** HostAdvice's expert review rates the Public Cloud 9.4/10 overall, with fast support (a sub-40-minute reply in their overnight test), strong CPU/memory benchmarks, ~10Gbps network throughput, and NVMe reads around 5GB/s. Trustpilot sits at a middling 3.4/5 — though on a sample of only 13 reviews, which is statistically noise more than signal. The pattern across long-running hosting forums is consistent praise for the network and DDoS protection, with occasional grumbles. Reasonable overall; just don't expect enterprise-hand-holding on advanced kernel tuning questions.

**Deployment is fast for cloud, slower for custom metal.** Cloud VMs deploy in minutes from weekly-updated official OS images. Custom bare-metal is the one place patience is required.

## Getting the backup job itself done

The server is only half the equation. On the software side, the standard playbook works fine on a Sharktech cloud node because you get root and full OS control:

1. **Pick your tool:** restic or Borg for deduplicated, encrypted, incremental backups; rsync over SSH for simple mirrors; Proxmox Backup Server if your primaries are Proxmox VMs; or the included Acronis add-on if you'd rather buy than build.
2. **Encrypt before it leaves your building.** Client-side encryption means the backup server never holds plaintext, which matters more when the box is rented.
3. **Use the platform's own snapshot scheduling** for crash-consistent point-in-time copies on the cloud side — it's built into the Virtuozzo panel, alongside backup plans and recovery points.
4. **Automate verification.** A backup you've never restored from is a hope, not a backup. Schedule monthly test restores; with $2/TB egress, testing is nearly free.
5. **Script the whole lifecycle via the OpenStack API** if you're running more than a couple of targets — the full compute, storage, and networking surface is API-accessible.

And remember the exit ramp: because you can export disk images at any time, your backup server itself can be backed up. Recursion jokes aside, that's a genuinely useful property when migrating providers later.

## Quick answers to common questions

**Is HDD storage too slow for backups?** No. Their published HDD performance is around 120MB/s sequential — far more than almost any upstream connection can saturate. NVMe matters for databases and live workloads, not archive targets.

**Can I run Windows on the backup server?** Yes, the cloud platform supports both Linux and Windows VMs, though Linux is the path of least resistance for backup tooling.

**Do I have to sign a contract?** No long-term contracts; monthly or hourly billing, and resources scale up or down from the panel without redeploying.

**What if I outgrow my plan?** Non-Enterprise plans cap out at their listed maximums; beyond that, upgrade tiers or move to Enterprise/Custom. Upgrades happen in-place.

**Which location should I pick?** Whichever is geographically separated from your primary infrastructure but still close enough to you for acceptable restore latency. Five US/EU cities are on the menu.

## The bottom line

For a "backup servers" shopper, the buying logic compresses to three sentences. Match the storage tier to the workload — HDD for archives, SSD only if you'll actually read it hot. Check egress before anything else, because restore day is the only day that counts. And don't rent from anyone who won't hand your data back.

On those three tests, a Sharktech Public Cloud VM is a well-shaped answer: ~$14/TB/month HDD at PAYG rates on top of a $39 entry plan, free unlimited inbound, $2/TB outbound beyond a 20TB allowance, five locations, DDoS protection included, and unrestricted image export. The lack of a refund policy is the main fine print — so start with a Small plan, prove your backup and restore cycle works, then scale. That's the entire decision, and now you have the numbers to make it. 👉 deploy a backup node and put your 3-2-1 rule's offsite copy somewhere that charges $2 a terabyte to give it back.

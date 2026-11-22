# Ticket 2: Office hardware specification

## 1. What was asked

From: Daniyar K. (Director)
Subject: computers for the new office

"We are opening the second office and need four computers: accountant, sales, web developer, designer. Budget 3,000,000 tenge for everything. Everyone says we should just buy the same four machines, is that right? Make sure they last a few years. Monitors included."

## 2. What was actually needed

Four identical machines is the wrong plan. The four people use the computer in four different ways, so the bottleneck is different for each one. The accountant needs reliability, the sales manager needs memory for browser tabs and video calls, the web developer needs memory and CPU threads for containers and builds, and the designer needs a graphics card and a good screen. If all four get the same machine, one of them gets too much and another gets too little. "Everyone says so" is not a reason, it only says that buying one model is easy.

What the request left out:
- The 3,000,000 tenge is "for everything", but it does not say if it includes the operating system licences, keyboards, mice, a UPS, a webcam and delivery. I assumed it includes all of them, so I put them into budget.csv and the grand total.
- "A few years" is not a number. I assumed 4 to 5 years, so I chose the AM5 platform with DDR5, which can take a newer CPU and more memory later.
- It does not say which software each person uses. I assumed Windows 11 Pro for all four, because accounting software in Kazakhstan often needs Windows and the designer's tools run best on it. For the web developer, Linux would also work, but one licensed system for all is easier to support.
- It does not say who builds the computers, who gives the warranty and who fixes them after that.
- It does not say if the old office has monitors or peripherals that can be reused.

What I would ask Daniyar: which software each person really uses, how many years he means, whether the budget includes licences and peripherals, and whether the company has someone to assemble and support the machines. Where I did not have an answer, I used the assumptions above.

## 3. What I did

I started from the four roles and wrote for each one the load, the bottleneck and the trade-off (builds.md). Then I chose the parts so the money goes to the bottleneck, and checked compatibility with PCPartPicker (socket, memory type, M.2 slot, power). All prices are in budget.csv with the vendor link and the date I saw the price. The platform is the same for all four (AM5, DDR5, NVMe) but the parts inside are different.

Alternatives I did not take: identical machines for everyone (wastes money on the accountant and underpowers the designer); Intel LGA 1700 with DDR4 (cheaper memory, but a dead end for upgrades); a more expensive GPU for the designer (does not fit in the budget).

Check against the hard requirements: every machine has at least 16 GB of memory (16 GB for the accountant, 32 GB for the other three), a 1 TB SSD, a licensed Windows 11 Pro and a monitor. The grand total is 2 894 499 tenge, which is under 3 000 000 (105 501 tenge left). Totals per role: accountant 563 696, sales 659 557, web developer 704 928, designer 966 318.

Answer 1. Which machine will I upgrade first in eighteen months? The web developer's machine: the memory, from 32 gb to 64 gb. Containers, VMs and databases grow every year and memory is the first thing to run out. The board has two free slots, so it costs only two more sticks and 5 minutes.

Answer 2. Pre-built machines. I found one ready machine for each role. The prices, links and dates are in the table below. A ready machine does not include a monitor, and Windows is not always included, so for a fair comparison I compare only the computer itself plus Windows 11 Pro (46 800 tenge, from my budget.csv) when the pre-built one has no system.

| Role | Pre-built model | Vendor | Price KZT | Date |
|------|-----------------|--------|-----------|------|
| accountant | APEX AMD Ryzen 5 3400G, Radeon RX Vega 11, 16 GB, SSD 1000 GB, no OS (https://kaspi.kz/shop/p/sistemnyi-blok-apex-amd-ryzen-5-3400g-amd-radeon-rx-vega-11-16-gb-ssd-1000-gb-os-ne-ustanovlena-132413047/) | kaspi.kz | 179 990 | 2026-11-22 |
| sales | Tehnicom Intel Core i5-13400, UHD 730, 32 GB, SSD 1000 GB, Win 11 (https://kaspi.kz/shop/p/tehnicom-art39676-belyi-170800253/) | kaspi.kz | 371 429 | 2026-11-22 |
| web developer | Tehnicom Intel Core i7-12700, UHD 770, 32 GB, SSD 1000 GB, Win 11 (https://kaspi.kz/shop/p/sistemnyi-blok-tehnicom-intel-core-i7-12700-intel-uhd-graphics-770-32-gb-ssd-1000-gb-win-11-171145692/) | kaspi.kz | 413 514 | 2026-11-22 |
| designer | WW-KZ gaming PC: Ryzen 5 7500F, RTX 5060 Ti 8 GB, 1 TB SSD, 32 GB DDR5 (https://shop.kz/offer/kompyuter-igrovoy-ww-kz-amd-ryzen-5-7500f-rtx-5060-ti-8-gb-1-tb-ssd-32-gb-ddr-5/) | shop.kz | 899 190 | 2026-11-22 |

Comparison with my builds (the computer only, with Windows 11 Pro, without monitor and peripherals):
accountant: my build 484 406, pre-built 179 990 + 46 800 for Windows = 226 790. Pre-built is cheaper by about 257 600.
sales: my build 560 546, pre-built 371 429 (Windows is included). Pre-built is cheaper by about 189 100.
web developer: my build 598 136, pre-built 413 514 (Windows is included). Pre-built is cheaper by about 184 600.
designer: my build 859 526, pre-built 899 190 + 46 800 for Windows = 945 990. My build is cheaper by about 86 500.

So for the three office roles the ready machines are clearly cheaper, and only for the designer building is cheaper. The ready machines are cheaper because they use an older platform and are sold in bulk, and my builds use AM5 with DDR5, which is expensive now. The accountant machine is also weak: the Ryzen 5 3400G is an old four core processor from 2019 on the old AM4 platform, and I would not take it for a few years.

What the company gains by not building: a warranty on the whole machine (the Tehnicom ones say 2 years), one company to call, no time spent on assembling and testing, Windows already installed and set up, and a lower price for the office roles.
What it loses: the choice of parts, the upgrade path (the old platforms have no new processors and DDR4 memory cannot be reused on AM5), the quality of parts I know (the power supply and the case in a ready machine are usually cheap), and for the designer a higher price.
My decision is to build the machines, because the company wants them to last a few years and the AM5 platform can be upgraded, but I must say that this costs more, and for the accountant the pre-built is a real alternative if the budget gets tighter.

Answer 3. The designer will complain first, about the graphics card: the RTX 5060 has 8 gb of video memory, and with large images and a long project in Photoshop or video it will run out of video memory and the work will get slow. I chose this trade-off on purpose to stay in budget, and I think this is where it will hurt first.

## 4. Evidence

- builds.md: the load, bottleneck and trade-off for each of the four machines.
- budget.csv: every part with the price in tenge, the vendor link and the date, with a total per role and a grand total. It proves the total is under 3,000,000 tenge.
- evidence/pcpartpicker-links.md: one PCPartPicker list per build. It proves the parts are compatible. PCPartPicker does not show tenge, so it is not used for prices.
- evidence/vendor-quotes/: a screenshot of each price with the site and the date visible. It proves the price is real and from a vendor that delivers in Kazakhstan.

## 5. What I would do differently

I would ask the director the questions in section 2 before choosing anything, because the biggest unknown (is the OS and peripherals included in the budget) changes the total by several hundred thousand tenge. I would also take the screenshots on the same day as the prices, because memory and SSD prices change within a month.

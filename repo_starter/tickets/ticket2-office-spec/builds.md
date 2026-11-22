# Ticket 2: four builds

All four use the same platform (AM5, DDR5, M.2 NVMe, mATX board). The platform is the same so one spare part and one way of fixing things works for the whole office. The money inside each machine is different, because the loads are different. Prices are in budget.csv.

Compatibility check for all four: Ryzen 5 7600X and Ryzen 7 7700 are AM5 and the MSI PRO B650M-P board is AM5. The board takes DDR5 only and the memory is DDR5. The board has an M.2 slot for the NVMe SSD. Total power is far below the power supply rating (about 200 W for the 7600X builds, about 350 W for the designer with the RTX 5060).

## Build 1: accountant

Load: accounting software and Excel all day, 8 hours, a few big spreadsheets and a browser. Mostly one thing at a time.
Bottleneck: not the CPU and not the memory. Reliability and the data are what run out first: a power cut or a dead disk in the middle of a report closing costs more than a slow machine.
Trade-off: the cheapest CPU and only 16 gb of memory (the minimum), and the saved money went to a UPS and a decent 24 inch screen. The memory has two free slots later, so 16 gb can be doubled in a few minutes.
Total: see budget.csv, rows for accountant.

## Build 2: sales manager

Load: CRM in a browser, 30 to 40 tabs, Excel, about four hours of video calls a day, often all at once.
Bottleneck: memory. Forty tabs plus a video call is around 11 to 12 gb, so on 16 gb it would start swapping in the middle of a call (I saw the same thing in Lab 6).
Trade-off: Ryzen 5 and not Ryzen 7, and the saved money went to 32 gb instead of 16 gb and a webcam and a headset for the calls. The CPU is mostly idle in this role.
Total: see budget.csv, rows for sales.

## Build 3: web developer

Load: code editor, several browsers, Docker containers, a local database and sometimes a virtual machine, all at the same time.
Bottleneck: memory first, then CPU threads. Containers and a VM each take a fixed slice of memory, and builds use all the cores.
Trade-off: Ryzen 7 7700 (8 cores, 16 threads) for the builds, but I did not buy a separate graphics card because the CPU has a small integrated GPU that is enough for a code editor. The memory is 32 gb in two sticks, and two slots stay free for 64 gb later.
Total: see budget.csv, rows for webdev.

## Build 4: designer

Load: large images and sometimes video in Adobe tools or Affinity, colour work for several hours.
Bottleneck: the graphics card (video memory) and the screen. Big files make the GPU and its memory run out, and a screen with bad colours makes all the other work useless.
Trade-off: I put the most money here. Ryzen 5 (the CPU is not the limit) and a mid GeForce RTX 5060 with 8 gb, but a 27 inch QHD monitor with good colours. I did not choose a more expensive GPU, because the budget has no space for it.
Total: see budget.csv, rows for designer.

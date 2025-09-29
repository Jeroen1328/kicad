## The "KickCard": External Kickstart Freedom for the Amiga 500 🚀

The **KickCard** project was born out of a common frustration among **Amiga 500** enthusiasts: the hassle and motherboard variations involved in upgrading the internal **Kickstart ROM**. This external interface, connected via the Amiga 500's side expansion slot, offers a clean, versatile, and non-intrusive solution for using different Kickstart ROMs.

### Status
This project is **currently under construction** and has **not been tested** in a physical unit. The author is uncertain if a functional board will ultimately be built.

---

### Overcoming Internal ROM Challenges

Older Amiga 500 revisions required specific circuitry, often involving **pull-up resistors** on the data bus, to properly support certain **EPROMs** when used as the Kickstart ROM. Installing a new ROM meant opening the computer, desoldering (sometimes), and potentially adding components.

The KickCard eliminates this complexity. By providing an **external, standardized interface**, it bypasses the finicky requirements of various motherboard revisions, making ROM upgrades plug-and-play. Plus, you never have to **open your Amiga 500** just to swap a Kickstart version!

---

### Features and Functionality

Beyond its core function, the KickCard is a feature-rich expansion:

* **Dual ROM Support:** It accommodates two types of external ROMs: **ROM1** (two flash ROMs) and **ROM2** (a 40/42 pin ROM), selectable via a **DIP switch** setting (**ROMSEL**).
* **External Enable/Disable:** A DIP switch (**SW\_KICK**) allows the user to easily **enable or disable** the external KickCard ROM, reverting to the internal ROM if present.
* **Bank Selection:** For the larger **ROM2**, address lines **A18 and A19** can be set via DIP switches, enabling the selection of different banks within the ROM.
* **Monitoring and Control (Optional):**
    * **Voltmeters:** Three optional **voltmeters** display the Amiga's crucial **+5V, +12V, and -12V** power supply lines, providing immediate diagnostic feedback.
    * **NMI/Reset Buttons:** Optional buttons for **$\overline{\text{INT7}}$ (Non-Maskable Interrupt)** and **System Reset** offer convenient control without needing a software-based utility.

---

### How the KickCard Works (The Logic)

The core of the KickCard's operation relies on a **GAL (Generic Array Logic)** chip and the Amiga's memory management architecture:

1.  **Initial Activation:** When the Amiga system starts up or is reset, a specific register, **$\overline{\text{OVL}}$ (Overlay)**, becomes active.
2.  **Memory Access Interception:** When the CPU addresses the **Kickstart memory range** ($000000-\text{07FFFF}$ and $\text{F80000}-\text{FFFFFF}$) with $\overline{\text{OVL}}$ active or ($\text{F80000}-\text{FFFFFF}$) with $\overline{\text{OVL}}$ not active, the GAL asserts the **$\overline{\text{OVR}}$ (Override)** signal.
3.  **Gary Disablement:** The asserted $\overline{\text{OVR}}$ signal prevents the **GARY** (Gate Array) chip—which typically manages memory arbitration—from responding to the bus, effectively taking control of the Kickstart address space.
4.  **ROM Selection:** Simultaneously, the GAL uses the logic to select **one of the two external ROMs** (ROM1 or ROM2) based on the **ROMSEL** switch setting and the selected bank (A18/A19).
5.  **Deactivation:** The $\overline{\text{OVL}}$ remains active until the CPU executes a write access to the address of the **CIA (Complex Interface Adapter)** chips. This action typically signals the completion of the basic system startup, and the GAL then makes the $\overline{\text{OVL}}$ register **inactive**, returning memory control to the standard system configuration.
6.  **when one of the roms is selected, the GAL also asserts the $\overline{\text{DTACK}}$ signal to tell the 68000 to complete the read cycle.
7.  the GAL also uses the CCK and CCKQ to create the 7Mhz clock as not all Amiga revisions supplies this on the expansion bus. however, the code in the GAL doesn't use the CLK_IN signal, this was used in a project that I used as reference)
 
In essence, the KickCard *tricks* the Amiga into loading the Kickstart from the expansion port during the critical boot sequence, providing a robust and flexible ROM replacement solution.

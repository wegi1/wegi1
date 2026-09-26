# 👑 Bare-Metal ARM Cortex-M Engineering: The Ultra-Minimalist Assembly Collection

### Choose your language / Wybierz język:
- [🇬🇧 English Master Overview](#-english-master-overview)
- [🇵🇱 Zbiorczy Opis po Polsku](#-zbiorczy-opis-po-polsku)

---

## 🇬🇧 English Master Overview

Welcome to the ultimate proving ground of raw ARM assembly programming, where every single byte is fought for, high-level abstractions are discarded, and hardware datasheets are treated as law. 

This collection showcases two record-breaking, hardware-validated bare-metal implementations designed to do one fundamental thing: blink an LED with the smallest binary footprint humanly possible on ARM architecture.

### 📊 Side-by-Side Comparison

| Feature / Metric | 🛸 STM32F401CC (Black Pill) | 🚀 STM32F103C8 (Blue Pill) |
| :--- | :--- | :--- |
| **Total Binary Size** | **Exactly 34 Bytes** 📉 | **Exactly 38 Bytes** 📈 |
| **CPU Architecture** | ARM Cortex-M4 (Thumb-2) | ARM Cortex-M3 (Thumb-2) |
| **LED Pin Location** | Port C, Pin 13 (Active LOW) | Port C, Pin 13 (Active LOW) |
| **RCC Base Address** | `0x40023800` | `0x40021000` (Enabler: `0x40021004`) |
| **Primary Hardware Hack**| Stack Pointer Peripheral Hijacking | Instruction Injection (Data Masked as `ADD`) |
| **Direct Repository Link**| [Go to Black Pill 34B](https://github.com) | [Go to Blue Pill 38B](https://github.com) |

---

### 🧠 The Architectural Showdown: Why the 4-Byte Difference?

People on Reddit often ask: *Why does the Blue Pill take 38 bytes, while the newer, more complex Black Pill fits into just 34 bytes?* The answer lies in the beautiful structural differences between the Cortex-M3 and M4 register mappings and how data constraints force defensive engineering:

#### 1. The Clock Control (RCC) Gap
- **On the Black Pill (F401):** The Reset and Clock Control base address is `0x40023800`. By hijacking the initial Stack Pointer (MSP) vector to point exactly here, the CPU can immediately execute store instructions using the SP register as a direct base pointer.
- **On the Blue Pill (F103):** The APB2 peripheral clock enabler register sits at `0x40021004`. While we can hijack the SP to point here, the relative distance to the GPIOC configuration memory space is wider and mapped differently, changing the displacement offset budgets.

#### 2. The GPIOC Configuration Challenge (The `ADD` Instruction Cloaking)
- To configure Port C Pin 13 as a Push-Pull output on the Blue Pill, we must load a complex 32-bit bitmask (`0x44144444`) into the configuration register. 
- Passing this raw literal data in line typically crashes the CPU via a `HardFault` because the processor pipeline tries to decode the data as active code.
- **The Hack:** Instead of wasting bytes using an unconditional branch instruction (`B`) to jump over the data block, the bitmask was meticulously engineered so that its raw binary sequence perfectly mirrors valid, harmless ARM Thumb-2 instructions (`add r4, r8` and `add r4, r2`). The CPU executes these dummy adds blindly, steps through the data safely, maintains strict 32-bit memory alignment, and saves 2 bytes of branching overhead!

---

## 🇵🇱 Zbiorczy Opis po Polsku

# 👑 Inżynieria Bare-Metal ARM Cortex-M: Kolekcja Ultra-Minimalistycznego Asemblera

Witamy na poligonie doświadczalnym czystego programowania w asemblerze ARM. Tutaj walczy się o każdy pojedynczy bajt, odrzuca wysokopoziomowe abstrakcje, a dokumentacja techniczna układu scalonego staje się prawem.

Ta kolekcja zawiera dwie rekordowo małe, zweryfikowane sprzętowo implementacje bare-metal, których celem jest realizacja fundamentalnego zadania: migania diodą LED przy użyciu najmniejszego możliwego pliku binarnego, jaki da się osiągnąć na architekturze ARM.

### 🧠 Architektoniczne starcie: Skąd różnica 4 bajtów?

Na forum Reddit często pojawia się pytanie: *Dlaczego Blue Pill potrzebuje 38 bajtów, podczas gdy nowszy i bardziej złożony Black Pill mieści się w zaledwie 34 bajtach?* Odpowiedź kryje się w różnicach mapowania rejestrów oraz ograniczeniach danych, które wymusiły zastosowanie skrajnie odmiennych trików:

#### 1. Różnica w kontroli zegarów (RCC)
- **W Black Pill (F401):** Bazowy adres rejestrów RCC to `0x40023800`. Dzięki "porwaniu" początkowego Wskaźnika Stosu (MSP) we wektorze resetu i ustawieniu go dokładnie na ten adres, procesor może natychmiast zapisywać dane konfiguracyjne, traktując rejestr SP jako darmowy, predefiniowany wskaźnik bazowy.
- **W Blue Pill (F103):** Rejestr włączania zegara APB2 znajduje się pod adresem `0x40021004`. Choć tu również przejmujemy SP, to dystans relatywny i przesunięcie (offset) do przestrzeni adresowej GPIOC wymaga innej matematyki adresowej.

#### 2. Wyzwanie konfiguracyjne GPIOC (Kamuflaż danych jako instrukcje `ADD`)
- Aby ustawić Pin 13 Portu C jako wyjście Push-Pull na układzie F103, należy załadować do rejestru konfiguracyjnego specyficzną 32-bitową maskę bitową: `0x44144444`.
- Zwykłe umieszczenie tak dużej stałej w potoku kodu powoduje zawieszenie procesora (`HardFault`), ponieważ rdzeń próbuje dekodować te losowe dane jako instrukcje maszynowe.
- **Rozwiązanie:** Zamiast marnować cenne bajty na instrukcję skoku bezwarunkowego (`B`), która ominęłaby ten blok danych, maska bitowa została zaprojektowana tak, aby jej binarny zapis odpowiadał poprawnym, całkowicie nieszkodliwym instrukcjom asemblera Thumb-2 (`add r4, r8` oraz `add r4, r2`). Procesor "ślepo" wykonuje te puste dodawania, przechodzi przez blok konfiguracyjny bez generowania błędu, zachowuje idealne wyrównanie pamięci do 32 bitów i oszczędza 2 bajty na skoku!

---

---

## 🛠️ My Technology Stack & Active Repositories / Moje Technologie i Projekty

### 🚀 Featured Low-Level Projects / Wyróżnione Projekty Niskopoziomowe:
* 🛸 **BLACK-PILL-ASM-BLINK-34-BYTES:** https://github.com/wegi1/BLACK-PILL-ASM-BLINK-34-BYTES
* ⚡ **BLUE-PILL-ASM-BLINK-38-BYTES:** github.com/wegi1/BLUE-PILL-ASM-BLINK-38-BYTES

### 🎮 Retrocomputing & Demoscene Gists / Kod dla Commodore 64:
* 🕹️ **BMP256VDOTS (256 Vector Dots Ball):** gist.github.com/wegi1/8577c92f4e2e89f2420ebe841a61c46f
* 💾 **128loader (128 Vector Dots for IRQ Loaders):** gist.github.com/wegi1/6d3054dcf82428ab28de97317ddb0c89

---
*Bending silicon to our will since 2015. No HAL, no startup templates, zero bloat.* 😉


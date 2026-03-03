# GitHub Copilot Agents

Materiały w tym repozytorium zostały udostępnione, aby pokazać możliwe wykorzystanie **agentów** w Visual Studio Code wraz z subskrypcją **GitHub Copilot**.

## Czym są agenci?

Agenci pozwalają na:

- **Wygenerowanie architektury** rozwiązania na podstawie wymagań
- **Dekompozycję zadań** na mniejsze, wykonalne kroki
- **Wykonanie pracy** przez wyspecjalizowanych agentów koordynowanych przez agenta `Orchestrator`

Agent `Orchestrator` pełni rolę koordynatora — przyjmuje złożone zadanie, dzieli je na fazy i deleguje pracę do wyspecjalizowanych agentów takich jak `Planner`, `Developer`, `Designer`, `Architect` czy `Decomposer`.

## Przykłady wykorzystania

W katalogu [.github/prompts](.github/prompts) znajdują się przykłady promptów pokazujących praktyczne wykorzystanie agentów:

| Prompt                                               | Opis                                     |
| ---------------------------------------------------- | ---------------------------------------- |
| `1.architecture.prompt.md`                           | Generowanie architektury rozwiązania     |
| `2.decompose.prompt.md`                              | Dekompozycja zadań na mniejsze kroki     |
| `3a.implement-subtask.prompt.md`                     | Implementacja podzadania (wariant A)     |
| `3b.implement-subtask.prompt.md`                     | Implementacja podzadania (wariant B)     |
| `3c.implement-story.prompt.md`                       | Implementacja całej historii użytkownika |
| `copilot-instructions-blueprint-generator.prompt.md` | Generator instrukcji dla Copilota        |

## Zastosowanie w praktyce

Agenci byli aktywnie wykorzystywani do tworzenia funkcjonalności na stronie [https://weteach.app](https://weteach.app). Obecnie na platformie dostępny jest produkt **Speaker** — narzędzie pomagające w nauce języka angielskiego.

## Wymagania

- Visual Studio Code
- Subskrypcja GitHub Copilot

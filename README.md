# 🌶️🐟 Spicy Tuna

A task management app that changes with the user. Name will probably change.

**PHP:** 8.4  
**Laravel:** 12  
**Node:** 22  
**Asset Compiler:** Vite  
**Database:** SQLite  
**Frontend:** [Livewire v3](https://livewire.laravel.com)  
**Testing:** [Pest v4](https://pestphp.com/docs/installation)  
**Hosting:** TBD  
**Notable Packages:**
- [Essentials](https://github.com/nunomaduro/essentials)
- [Flux](https://fluxui.dev) - Livewire UI component library

## Table of Contents

- [About](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#about)
  - [The Problem](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#the-problem)
  - [The Solution](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#the-solution)
    - [Gamification Modes](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#gamification-modes)
    - [Tasks](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#tasks)
    - [Social Features](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#social-features)
  - [Target Audience](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#target-audience)
- [Getting Started](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#getting-started)
- [Continuous Integration](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#continuous-integration)
- [Laravel Boost](https://github.com/techenby/spicy-tuna?tab=readme-ov-file#laravel-boost)

## About

### The Problem

Existing gamified task apps like Finch and Streaks work great—until they don't. Once you've "beaten" the game (grown your Finch to adulthood) or broken a streak, the motivation vanishes. For people with 🧠🌶️, the novelty cliff is real: dopamine-seeking brains love new systems, but when the sparkle fades, the app becomes something to avoid rather than use. Broken streaks trigger shame spirals that make it even harder to come back.

### The Solution

Spicy Tuna offers **swappable gamification modes**. When one mode loses its appeal, switch to another—no guilt, no punishment, no starting over. The app adapts to your brain instead of demanding your brain adapt to it.

#### Gamification Modes

Starting with two modes (more to come):

- **Pet Raising** — Nurturing vibes. Complete tasks to care for your pet; daily activity helps it grow. When you return to this mode, you choose: continue with your existing pet, reset its progress, or start fresh with a new animal.
- **Streaks** — Competitive vibes. Classic streak tracking for those who thrive on maintaining momentum.

Mode switching is user-triggered (smarter detection for disengagement may come later).

#### Tasks

Supports daily habits, one-off to-dos, and recurring chores. Simple reward system: **1 task completion = 1 reward**. No weighting, no overthinking—just "did I do the thing?"

#### Social Features

- Share progress with others
- Accountability buddies
- Shared tasks (e.g., household chores that either partner can check off, both get credit)

### Target Audience

People with 🧠🌶️ (Autism, ADHD, AuDHD, and similar neurodivergent brains) who struggle with traditional gamified task apps.

## Getting Started

1. Clone Repo
2. Set PHP version to 8.5
3. `composer config http-basic.composer.fluxui.dev {licence holder} {license key}`
4. `composer install`
5. `npm install`
6. `cp .env.example .env`
7. `php artisan key:generate`
8. Link the directory to Valet or Herd

## Continuous Integration

Required GitHub Action secrets:

```
FLUX_USERNAME
FLUX_LICENSE_KEY
```

### Laravel Boost

To get started with Laravel Boost, run `php artisan boost:install`. This command publishes the guidelines for your chosen agent. Be sure to add all Boost-generated files to the `.gitignore` file.

Add any project rules that apply to all agents to the `.ai/guidelines` folder as `.md` or `.blade.php` files.

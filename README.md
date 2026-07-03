# photodialectic

A minimal personal site for small projects and ideas, built with Jekyll and hosted on GitHub Pages.

## Overview

This is a GitHub Pages repository that uses Jekyll for static site generation. The site features a clean, minimal design inspired by modern design systems, with a focus on typography, thin borders, and generous whitespace.

**Live site:** [photodialectic.github.io](https://photodialectic.github.io/)

## Features

- **Light/Dark Theme Toggle:** Automatic system preference detection with manual override
- **Responsive Design:** Mobile-friendly layouts optimized for all screen sizes
- **Interactive Games:** Minesweeper, cryptogram puzzles, and XSS security game
- **Clean Minimal Design:** Helvetica typography, thin 1px borders, theme-aware colors

## Attribution

The XSS Security Game is inspired by and adapted from Google's original [XSS Game](https://xss-game.appspot.com/).

## Tech Stack

- **Jekyll:** Static site generator
- **GitHub Pages:** Hosting platform
- **Vanilla JavaScript:** Theme switching and game logic
- **CSS Variables:** Theme system for light/dark modes
- **Reveal.js:** Presentation framework (for talks/slides)

## Project Structure

```
.
├── _config.yml           # Jekyll configuration
├── _layouts/
│   ├── default.html      # Main layout with theme support
│   └── talk.html         # Reveal.js presentation layout
├── game/
│   ├── index.html        # Games listing page
│   ├── minesweeper/      # Minesweeper game
│   └── cryptogram/       # Cryptogram puzzle
├── talks/                # Presentation slides
└── index.html            # Homepage
```

## Local Development

Run the site locally using Docker:

```bash
docker run --rm \
    --volume "$PWD:/srv/jekyll" \
    --publish 4000:4000 \
    --publish 35729:35729 \
    jekyll/jekyll \
    jekyll serve --host 0.0.0.0 --livereload
```

Then visit [localhost:4000](http://localhost:4000)

Alternatively, if you have Jekyll installed locally:

```bash
bundle install
bundle exec jekyll serve --livereload
```

## Deployment

This site automatically deploys to GitHub Pages when changes are pushed to the `main` branch. GitHub Pages builds and serves the site at the configured custom domain.

## Theme System

The site uses CSS custom properties (variables) to support light and dark themes:

- Theme preference is saved to localStorage
- Falls back to system preference on first visit
- Smooth transitions between themes
- All games and pages respect the theme setting

## Design Philosophy

The design emphasizes:
- **Minimalism:** Clean layouts with plenty of whitespace
- **Typography:** Helvetica Neue for a classic, readable look
- **Borders:** Thin 1px borders instead of shadows or heavy styles
- **Responsiveness:** Mobile-first approach with progressive enhancement

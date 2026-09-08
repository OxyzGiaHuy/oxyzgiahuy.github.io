# Blog writing rules

These rules apply to every new blog post derived from a seminar slide deck.

## Slide visuals

- Include one or two inline visuals from the slide deck whenever the deck contains useful diagrams, pipelines, tables, plots or architecture figures.
- Prefer visuals that explain an idea or support an insight. Do not use title slides or decorative screenshots unless they provide context.
- Use the full slide when its layout is important. Crop only when a smaller region is clearer and the crop does not remove necessary labels or context.
- Store extracted images locally under `assets/images/blog/slides/` with a stable name such as `blog-11-slide-18-topic.jpg`.
- Place each image near the paragraph that explains it using the `blog-slide` figure pattern:

```html
<figure class="blog-slide">
  <img src="{{ '/assets/images/blog/slides/blog-NN-slide-XX-topic.jpg' | relative_url }}" alt="Short, meaningful description" loading="lazy">
  <figcaption>Slide XX: explain the important insight shown in the visual.</figcaption>
</figure>
```

- Keep the complete local PDF linked near the beginning of the post so readers can inspect the original slide deck.
- Run Git diff whitespace checks and `bundle exec jekyll build` after adding images and posts.

## Writing standard

The post should explain the slide rather than transcribe it. For every important visual, answer what it shows, why it matters and what a reader should learn from it. Keep technical terminology accurate, make uncertainty explicit and use the blog schedule in `_data/blog_schedule.yml` for dates.

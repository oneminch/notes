---
alias: SEO
---

- Google's Ranking Factors
    - **Backlinks** - links from a page on a website to another.
        - If other prominent websites link to the page, the information is considered to be well trusted.
    - **Search Intent** - the reason behind a search query.
    - **Content Depth** - the quality of content or the extent to which the content solves the user's query.

- **CTR (Click-Through Rate)**
    - The percentage of users who click on your search result after seeing it in the SERP.
    - A higher CTR can positively impact your rankings.
- **SERP (Search Engine Results Page)**
    - The page displayed by search engines in response to a user's query.
    - Understanding SERP features can help you optimize for different result types.
- **Bounce Rate**
    - The percentage of visitors who leave your site after viewing only one page.
    - A high bounce rate may indicate that your content doesn't match user intent or provide a good user experience.
- **Dwell Time**
    - The amount of time a user spends on your page before returning to the SERP.
    - Longer dwell times generally indicate more engaging content.

## Optimizations

- Basic rules for great SEO
    - Create high-quality and relevant content that incorporates target keywords naturally.
    - Render properly structured and formatted (semantic & bot-friendly) [[HTML]].
    - Ensure content loads fast.

### On-Page SEO

- Focuses on optimizing individual web pages for _search intent_:
    - _Title tags_ - Include target keywords in page titles (50-60 characters long).
    - _Meta descriptions_ - Write compelling summaries of page content.
    - _Header tags_ - Use keywords in H1, H2, etc., to structure content with headings and subheadings.
    - _Content optimization_ - Create high-quality, relevant content that naturally incorporates target keywords.
        - Optimize for readability as well.
            - Write in short sentences and paragraphs.
            - Use descriptive subheadings to make it easy skimming.
    - _URL structure_ - Use short, descriptive, keyword-rich URLs.
    - _Optimize images_ - Improve page speed by compressing images.
    - _Internal linking_ - Connect related pages within a site.
    - _Schema markup_ - Improve search engine understanding of a page's content and context.
- The goal of pages should be to satisfy the searcher's intent and content needs to address their expectations.

### Off-Page SEO

- Builds a site's authority through external factors:
    - _Link building_ - Acquire high-quality backlinks from reputable websites.
        - What makes a good backlink?
            - Relevance
            - Authoritativeness
            - Anchor text
            - `rel` attribute use
            - Link placement
    - _Social signals_ - Engage on social media platforms to increase brand visibility.
    - _Brand mentions_ - Encourage mentions of your brand across the web.
    - _Guest posting_ - Contribute content to other relevant websites in your industry.

### Technical SEO

- Ensures a website is crawlable and indexable:
    - _Site structure_ - Create a logical hierarchy for content.
    - _XML sitemaps_ - Submit a sitemap to help search engines discover pages.
    - _Page speed optimization_ - Improve loading times for better UX and SEO.
        - Utilize [[Caching]] and compression to improve load times.
        - Follow general [[Web Performance]] best practices.
    - _Mobile-friendliness_ - Ensure site is responsive and works well on all devices.
    - _Schema markup_ - Implement structured data to enhance rich snippets in SERPs.
    - _SSL certificate_ - Secure sites with HTTPS.
    - _Canonical tags_ - Inform search engines what the preferred URL is for a page.
        - Helps solve duplicate content issues. e.g. `http://*` -> `https://*` (canonical)
    - _`noindex`_ meta tag - Prevents pages from being indexed.

```html
<!-- ⛔ Avoid using 'noindex' for pages to be indexed -->
<meta name="robots" content="noindex" />
```

### Keyword Research

- Identifying relevant terms and phrases the target audience uses to search for content related to a website.
- How to choose keywords worth targeting?
    - Check if keywords have search demand.
    - Check the traffic potential of the topic.
        - More reliable than search volume.
    - Assess the business potential of keywords.
    - Match searcher's intent.
    - Assess ranking difficulty.
        - Main things to consider when it comes to assessing ranking difficulty of keywords:
            - Search intent
            - Metrics of top ranking pages / sites
            - Topical authority of top ranking sites

## Common SEO Mistakes

- Ignoring Mobile Optimization
    - A mobile-friendly design is essential for maintaining visibility in search results.
- Neglecting Meta Tags
- Low-Quality Content
    - Search engines favor high-quality, informative content that meets user needs.
    - Content that lacks depth or is poorly written can lead to high bounce rates and low engagement.
    - It's important to focus on creating valuable content rather than just optimizing for keywords.
- Keyword Stuffing
    - Overloading content with keywords in an unnatural way can lead to penalties from search engines.
    - Instead, keywords should be used strategically and naturally within the content.
- Poor Keyword Research
    - Targeting the wrong keywords or overly generic terms can limit visibility.
    - Focus on long-tail keywords that are specific to your audience and have less competition.
- Not Using Analytics
    - Regular tracking and analyzing helps understand and refine SEO strategies and improve overall effectiveness.
- Over-Reliance on Technical SEO
    - Balance the technical aspects as well as content quality and user experience for effective SEO.
- Creating Content in Isolation
    - Producing content without considering competitors or audience needs can lead to irrelevant or ineffective material.
    - Engage with customer feedback and market research to create relevant content.
- Not Optimizing for Page Speed
    - A slow-loading website can frustrate users and negatively impact rankings.
    - Don't ignore [[Web Performance|web perf]].
- Failing to Update Content
    - Outdated content can harm your rankings.
    - Regularly refreshing and updating existing content ensures that it remains relevant and valuable to users.

---

## Further

### Learn 🧠

- [SEO Course for Beginners (Ahrefs) (YouTube)](https://www.youtube.com/watch?v=xsVTqzratPs) ⭐

### Reads 📄

> [!quote]- Baretto (tiny.host) on SEO ⭐
> ![Baretto (tiny.host) on SEO](https://twitter.com/_baretto/status/1828723629246341538)

### Videos 🎥

![How Core Web Vitals affect SEO](https://www.youtube.com/watch?v=qIyEwOEKnE0)

![SEO for Developers in 100 Seconds](https://www.youtube.com/watch?v=-B58GgsehKQ)

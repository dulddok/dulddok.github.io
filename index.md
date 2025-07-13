---
title: Home
layout: home
nav_order: 0
comments: false
description: "덜똑의 기술 블로그 - 모니터링, 개발 팁, 기술 리뷰를 다루는 개발자 블로그입니다. 실무 경험과 문제 해결 방법을 공유합니다."
permalink: /
hero_body: "덜똑의 기술 블로그 - 모니터링, 개발 실무 팁, 그리고 다양한 기술 경험을 공유하는 공간입니다."
hero_heading: "기술과 경험을 나누는 개발자 블로그"
hero_ctas:
  - label: "최근 문서 보기"
    link: "#recent-docs"


---

# 덜똑Log
{: .fs-9 }

써보고 싶었던 기술들, 그리고 시행착오와 삽질의 흔적들을 기록합니다. 
{: .fs-6 .fw-300 }

[최근 문서 보기](#recent-docs){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
<!-- [GitHub 프로필](https://github.com/dulddok){: .btn .fs-5 .mb-4 .mb-md-0 } -->

---


[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/dulddok) 
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/dulddok/)

---

## 📝 최근 문서

<div id="recent-docs">

{% assign all_docs = site.pages %}
{% assign recent_docs = all_docs | slice: 0, 6 %}
{% for doc in recent_docs %}
  {% if doc.path contains 'docs/' and doc.path != 'docs/index.md' and doc.path != 'docs/test-giscus.md' %}
    <div class="recent-doc-item" style="margin-bottom: 1rem; padding: 1rem; border: 1px solid var(--border-color); border-radius: 6px;">
      <h3 style="margin: 0 0 0.5rem 0;">
        <a href="{{ doc.url | relative_url }}">{{ doc.title | default: doc.name | replace: '.md', '' | replace: '-', ' ' | capitalize }}</a>
      </h3>
      {% if doc.description %}
        <p style="margin: 0 0 0.5rem 0; color: var(--text-muted);">{{ doc.description }}</p>
      {% endif %}
      <small style="color: var(--text-muted);">
        📁 {{ doc.path | split: '/' | slice: 1, 2 | join: ' > ' }}
        {% if doc.date %}
          • 📅 {{ doc.date | date: "%Y년 %m월 %d일" }}
        {% endif %}
      </small>
    </div>
  {% endif %}
{% endfor %}

{% if recent_docs.size == 0 %}
  <p>아직 작성된 문서가 없습니다. 곧 새로운 내용으로 찾아뵙겠습니다! 🚀</p>
{% endif %}

</div>

---

{: .warning }
> This website documents the features of the current `main` branch of the Just the Docs theme. See [the CHANGELOG]({% link CHANGELOG.md %}) for a list of releases, new features, and bug fixes.

Just the Docs is a theme for generating static websites with [Jekyll]. You can write source files for your web pages using [Markdown], the [Liquid]{:target="_blank"} templating language, and HTML.[^1] Jekyll builds your site by converting all files that have [front matter] to HTML. Your [Jekyll configuration] file determines which theme to use, and sets general parameters for your site, such as the URL of its home page.

Jekyll builds this Just the Docs theme docs website using the theme itself. These web pages show how your web pages will look *by default* when you use this theme. But you can easily *[customize]* the theme to make them look completely different!

Browse the docs to learn more about how to use this theme.

## Getting started

The [Just the Docs Template] provides the simplest, quickest, and easiest way to create a new website that uses the Just the Docs theme. To get started with creating a site, just click "[use the template]"!

{: .note }
To use the theme, you do ***not*** need to clone or fork the [Just the Docs repo]! You should do that only if you intend to browse the theme docs locally, contribute to the development of the theme, or develop a new theme based on Just the Docs.

You can easily set the site created by the template to be published on [GitHub Pages] – the [template README] file explains how to do that, along with other details.

If [Jekyll] is installed on your computer, you can also build and preview the created site *locally*. This lets you test changes before committing them, and avoids waiting for GitHub Pages.[^2] And you will be able to deploy your local build to a different platform than GitHub Pages.

More specifically, the created site:

- uses a gem-based approach, i.e. uses a `Gemfile` and loads the `just-the-docs` gem
- uses the [GitHub Pages / Actions workflow] to build and publish the site on GitHub Pages

Other than that, you're free to customize sites that you create with the template, however you like. You can easily change the versions of `just-the-docs` and Jekyll it uses, as well as adding further plugins.

{: .note }
See the theme [README][Just the Docs README] for how to use the theme as a gem without creating a new site.

## About the project

Just the Docs is &copy; 2017-{{ "now" | date: "%Y" }} by [Patrick Marsceill](https://patrickmarsceill.com).

### License

Just the Docs is distributed by an [MIT license](https://github.com/just-the-docs/just-the-docs/tree/main/LICENSE.txt).

### Contributing

When contributing to this repository, please first discuss the change you wish to make via issue,
email, or any other method with the owners of this repository before making a change. Read more about becoming a contributor in [our GitHub repo](https://github.com/just-the-docs/just-the-docs#contributing).

#### Thank you to the contributors of Just the Docs!

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"></a>
  </li>
{% endfor %}
</ul>

### Code of Conduct

Just the Docs is committed to fostering a welcoming community.

[View our Code of Conduct](https://github.com/just-the-docs/just-the-docs/tree/main/CODE_OF_CONDUCT.md) on our GitHub repository.

----

[^1]: The [source file for this page] uses all three markup languages.

[^2]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Jekyll]: https://jekyllrb.com
[Markdown]: https://daringfireball.net/projects/markdown/
[Liquid]: https://github.com/Shopify/liquid/wiki
[Front matter]: https://jekyllrb.com/docs/front-matter/
[Jekyll configuration]: https://jekyllrb.com/docs/configuration/
[source file for this page]: https://github.com/just-the-docs/just-the-docs/blob/main/index.md
[Just the Docs Template]: https://just-the-docs.github.io/just-the-docs-template/
[Just the Docs]: https://just-the-docs.com
[Just the Docs repo]: https://github.com/just-the-docs/just-the-docs
[Just the Docs README]: https://github.com/just-the-docs/just-the-docs/blob/main/README.md
[GitHub Pages]: https://pages.github.com/
[Template README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[customize]: #customize
[use the template]: https://github.com/just-the-docs/just-the-docs-template/generate

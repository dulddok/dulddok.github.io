<p align="center">
    <h1 align="center">덜똑의 기술 블로그</h1>
    <p align="center">기술과 개발에 대한 생각을 나누는 공간입니다.<br>다양한 기술 이야기를 담고 있습니다.</p>
    <p align="center"><strong><a href="https://dulddok.github.io/">블로그 바로가기</a></strong></p>
    <br><br>
</p>

## 📝 블로그 소개

안녕하세요! 저는 **덜똑**입니다. 

이 블로그는 기술과 개발에 대한 다양한 경험과 지식을 공유하는 공간입니다.

### 🎯 주요 콘텐츠

- **기술 문서**
- **개발 팁**: 개발 과정에서 얻은 경험과 노하우
- **기술 리뷰**: 다양한 기술과 도구에 대한 리뷰
- **문제 해결**: 개발 및 운영 과정에서 마주한 문제들과 해결 방법

### 🛠️ 기술 스택

- **Jekyll**: 정적 사이트 생성기
- **Just the Docs**: Jekyll 테마
- **GitHub Pages**: 호스팅 플랫폼
- **Giscus**: 댓글 시스템 (GitHub Discussions 연동)

### 🚀 로컬 개발

이 블로그는 Jekyll을 기반으로 구축되었습니다. 로컬에서 개발하려면:

```bash
# 의존성 설치
bundle install

# 로컬 서버 실행
bundle exec jekyll serve

# Docker 사용 시
docker-compose up
```

사이트는 `http://localhost:4000`에서 확인할 수 있습니다.

## Usage

[View the documentation][Just the Docs] for usage information.

### Giscus Comments

This site includes Giscus comments system for GitHub Discussions. To configure:

1. **Enable/Disable globally**: Set `comments: false` in `_config.yml` to disable comments site-wide
2. **Disable on specific pages**: Add `comments: false` to the front matter of any page
3. **Automatic exclusions**: Comments are automatically disabled on:
   - Home page (`layout: home`)
   - 404 error page
   - Pages with `comments: false` in front matter
4. **Configuration**: Edit `_includes/components/giscus.html` to modify Giscus settings

The comments will appear at the bottom of pages and posts, allowing visitors to leave comments using their GitHub accounts.

## Contributing

Bug reports, proposals of new features, and pull requests are welcome on GitHub at https://github.com/just-the-docs/just-the-docs. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

### Submitting code changes:

- Submit an [Issue](https://github.com/just-the-docs/just-the-docs/issues) that motivates the changes, using the appropriate template
- Discuss the proposed changes with other users and the maintainers
- Open a [Pull Request](https://github.com/just-the-docs/just-the-docs/pulls)
- Ensure all CI tests pass
- Provide instructions to check the effect of the changes
- Await code review

### Design and development principles of this theme:

1. As few dependencies as possible
2. No build script needed
3. First class mobile experience
4. Make the content shine

## Development

To set up your environment to develop this theme: fork this repo, the run `bundle install` from the root directory.

A modern [devcontainer configuration](https://code.visualstudio.com/docs/remote/containers) for VSCode is included.

Your theme is set up just like a normal Jekyll site! To test your theme, run `bundle exec jekyll serve` and open your browser at `http://localhost:4000`. This starts a Jekyll server using your theme. Add pages, documents, data, etc. like normal to test your theme's contents. As you make modifications to your theme and to your content, your site will regenerate and you should see the changes in the browser after a refresh, just like normal.

When this theme is released, only the files in `_layouts`, `_includes`, and `_sass` tracked with Git will be included in the gem.

## License

The theme is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

[^2]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Jekyll]: https://jekyllrb.com
[Just the Docs Template]: https://just-the-docs.github.io/just-the-docs-template/
[Just the Docs]: https://just-the-docs.com
[Just the Docs repo]: https://github.com/just-the-docs/just-the-docs
[GitHub Pages]: https://pages.github.com/
[Template README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[use the template]: https://github.com/just-the-docs/just-the-docs-template/generate

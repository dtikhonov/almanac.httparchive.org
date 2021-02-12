---
#See https://github.com/HTTPArchive/almanac.httparchive.org/wiki/Authors'-Guide#metadata-to-add-at-the-top-of-your-chapters
title: Сжатие
description: Глава "Сжатие" издания Web Almanac за 2020 год освещает HTTP сжатие, алгоритмы, типы данных, первостороннее и третьестороннее сжатие и перспективы.
authors: [mo271, veluca93, sboukortt, jyrkialakuijala]
reviewers: [paulcalvano]
analysts: [AbbyTsai]
editors: [exterkamp]
translators: [dtikhonov]
jyrkialakuijala_bio: Юрки Алакуяла -- активный член open source software сообщества и исследователь сжатия данных. Юрки работает в <span lang="en">Google</a> как технический руководидель.  Его недавно опубликовфанные работы включают <span lang="en">Zopfli</span>, <span lang="en">Butteraugli</span>, <span lang="en">Guetzli</span>, <span lang="en">Gipfeli</span>, <span lang="en">WebP lossless</span>, <span lang="en">Brotli</span>, <span lang="en">JPEG XL</span> форматы и алгоритмы сжатия, а также два алгоритма хаширования&colon; CityHash and HighwayHash. До <span lang="en">Google</span>, он разрабатывал программное обеспечение для нейрохирургии и радиотерапии.
sboukortt_bio: Сами присоединился к Google по окончании учебы по математике инженерии. После нескольких лет интереса(?) в сжатии, он в конце концов начал работать над этим в полную силу в 2018.
mo271_bio: Мориц Фиршинг -- инженер-программист в <span lang="en">Google Switzerland</span>, где он работает над прогрессивными форматами изображений и сжатием шрифтов. До того, Мориц занимался исследовательсокой деятельностью, изучая политопы.
veluca93_bio: Лука Версари -- инженер-программист в <span lang="en">Google</span>, где он работает над <a href="https://gitlab.com/wg1/jpeg-xl">JPEG XL</a>. Он заканчивает докторскую по сжатию графов и имеет математическое образование.
discuss: 2055
results: https://docs.google.com/spreadsheets/d/1NKbP4AqMkgCNCsVD3yLhO2d0aqIsgZ7AGLEtUDHl9yY/
featured_quote: Using HTTP compression makes a website load faster and therefore guarantees a better user experience.
featured_stat_1: 23%
featured_stat_label_1: Compressed responses which use Brotli
featured_stat_2: 77%
featured_stat_label_2: Compressed responses which use Gzip
featured_stat_3: 74%
featured_stat_label_3: Websites that pass the Lighthouse audit with maximum score on text compression
---

## Вступление

Использование сжатия данных в <span lang="en">HTTP</span> позволяет вебсайту загрузиться быстрее и, таким образом, гарантирует улучшенный опыт пользователя. <span lang="en">HTTP</span> без сжатмия ухудшает опыт пользователя, может замедлить темп роста веб-сервиса и влияет на поисковый рейтинг.  Эффективное использовение сжатия данных может уменьшить [page weight](./page-weight), улучшить [web performance](./performance), и значит является важной частью [поисковой оптимизации](./seo).

В то время, как сжатие с потерями зачастую приемлемо для изображений и других [медийных](./media) типов, для текста нужно использовать компрессию без потерь, дабы воспроизвести исходный текст в точности после расширения.

## Какие типы данных нужно сжимать?

Для большинства текстовых ресурсов, таких как [HTML](./markup), [CSS](./css), [JavaScript](./javascript), JSON или SVG, а также для определенных нетекстовых форматов типа woff, ttf и icj, использовние сжатия рекомендуется.

{{ figure_markup(
  image="compession-methods-by-content-type.png",
  caption="Методы сжатия ресурсов разных типов",
  description="A stacked bar chart showing the usage rate of different compression algorithms broken down by the content type. The stacked bars divide up the use of Brotli, Gzip, and no compression. `text/html` is the only content type that is compressed less than 50% of the time. `application/json` and `image/svg+xml` are each approximately 64% compressed. `text/css` and `application/javascript` are each approximately 85% compressed. `application/x-javascript` and `text/javascript` are greater than 90% compressed.",
  chart_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vTxUj8-0vKTqPAblIXqekSbiRh1D1lEuA3gVD9w23qwGPtJRE8FbgrURfPAgfFZX2l0t84Wy5ZAGqzR/pubchart?oid=1658254159&format=interactive",
  sheets_gid="107138856",
  sql_file="19_01.type_of_content_encoding.sql"
  )
}}

Этот график показывает, какой процент ответов определенных типов ресурсов используют сжатие <span lang="en">Brotli</span>, <span lang="en">Gzip</span> или же не используют сжатие.
Удивительно то, что хотя все эти типы ресурсов выигрывают от сжатия, процетный диапазон широко варьиерутся между разными типами: только 44% из `text/html` ресурсов сжимаются, тогда как из ресурсов типа `application/x-javascript` сжимаются 93%.

Сжатие изображений не так выгодно и широко не применяется. Данные показывают, что процент изображений в ответах, которые используют сжатие, очень мал: меньше 4%. Чтобы узнать больше о не-текстовых ресурсах, смотрете главу [Media](./media).

{{ figure_markup(
  image="http-compression-methods-for-image-types.png",
  caption="Compression methods for image types on desktop.",
  description="This breaks down what compression methods, if any, are used for all the content types which are images. For all three image types, i.e. jpeg, png and gif, around 96.5% use no compression is used.",
  chart_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vTxUj8-0vKTqPAblIXqekSbiRh1D1lEuA3gVD9w23qwGPtJRE8FbgrURfPAgfFZX2l0t84Wy5ZAGqzR/pubchart?oid=1287110333&format=interactive",
  sheets_gid="449339162",
  sql_file="19_01.type_of_content_encoding.sql"
  )
}}

## Как использовать <span lang="en">HTTP</span>-сжатие? {как-использовать-HTTP-сжатие}

To reduce the size of the files that we plan to serve one could first use some minimizers, e.g. [HTMLMinifier](https://github.com/kangax/html-minifier), [CSSNano](https://github.com/ben-eb/cssnano), or [UglifyJS](https://github.com/mishoo/UglifyJS2). However bigger gains are expected from using compression.

There are two ways of doing the compression on the server side:

  - Precompressed (compress and save assets ahead of time)
  - Dynamically Compressed (compress assets on-the-fly after a request is made)

Since precompression is done beforehand, we can spend more time compressing the assets. For dynamically compressed resources, we need to choose the compression levels such that compression takes less time than the time difference between sending an uncompressed versus a compressed file. This difference is borne out when looking at compression level recommendations for both methods.

<figure>
  <table>
    <thead>
      <tr>
        <td></td>
        <th scope="col">Brotli</th>
        <th scope="col">Gzip</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Precompressed</td>
        <td>11</td>
        <td>9 or Zopfli</td>
      </tr>
      <tr>
        <td>Dynamically compressed</td>
        <td>5</td>
        <td>6</td>
      </tr>
    </tbody>
  </table>
  <figcaption>{{ figure_link(caption="Recommended compression levels to use.") }}</figcaption>
</figure>

Currently, practically all text compression is done by one of two HTTP content encodings: [Gzip](https://tools.ietf.org/html/rfc1952) and [Brotli](https://github.com/google/brotli). Both are widely supported by browsers: [can I use Brotli](https://caniuse.com/?search=brotli)/[can I use Gzip](https://caniuse.com/?search=gzip)

When you want to use Gzip, consider using [Zopfli](https://en.wikipedia.org/wiki/Zopfli), which generates smaller Gzip compatible files. This should be done especially for precompressed resources, since here the greatest [gains are expected](https://cran.r-project.org/web/packages/brotli/vignettes/brotli-2015-09-22.pdf). See this [comparison between Gzip and Zopfli](https://blog.codinghorror.com/zopfli-optimization-literally-free-bandwidth/) that takes into account different compression levels for Gzip.

Many [popular servers](https://en.wikipedia.org/wiki/HTTP_compression#Servers_that_support_HTTP_compression) support dynamically and/or pre-compressed HTTP and many of them support [Brotli](https://en.wikipedia.org/wiki/Brotli).

## Нынешнее состояние <span lang="en">HTTP</span>-сжатия {нынешнее-состояние-HTTP-сжатия}

Approximately 60% of HTTP responses are delivered with no text-based compression. This may seem like a surprising statistic, but keep in mind that it is based on all HTTP responses in the dataset. Some content, such as images, will not benefit from these compression algorithms and is therefore not often used, as shown in figure 19.2.

<figure>
  <table>
    <thead>
      <tr>
        <th scope="col">Content Encoding</th>
        <th scope="col">Desktop</th>
        <th scope="col">Mobile</th>
        <th scope="col">Combined</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><em>No text compression</em></td>
        <td class="numeric">60.06%</td>
        <td class="numeric">59.31%</td>
        <td class="numeric">59.67%</td>
      </tr>
      <tr>
        <td>Gzip</td>
        <td class="numeric">30.82%</td>
        <td class="numeric">31.56%</td>
        <td class="numeric">31.21%</td>
      </tr>
      <tr>
        <td>Brotli</td>
        <td class="numeric">9.10%</td>
        <td class="numeric">9.11%</td>
        <td class="numeric">9.11%</td>
      </tr>
      </tr>
      <tr>
        <td><em>Other</em></td>
        <td class="numeric">0.02%</td>
        <td class="numeric">0.02%</td>
        <td class="numeric">0.02%</td>
      </tr>
    </tbody>
  </table>
  <figcaption>{{ figure_link(caption="Adoption of compression algorithms.", sheets_gid="1365871671", sql_file="19_01.type_of_content_encoding.sql") }}</figcaption>
</figure>

Of the resources that are served compressed, the majority are using either Gzip (77%) or Brotli (23%). The other compression algorithms are used infrequently.

{{ figure_markup(
  image="compression-algorithms-for-http-responses.png",
  caption="Compression algorithm for HTTP responses.",
  description="A bar chart showing the usage rates of different compression algorithms for HTTP responses. 77.39% of HTTP responses that use compression employ the Gzip algorithm, 22.59% use Brotli, and 0.03% use some other method.",
  chart_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vTxUj8-0vKTqPAblIXqekSbiRh1D1lEuA3gVD9w23qwGPtJRE8FbgrURfPAgfFZX2l0t84Wy5ZAGqzR/pubchart?oid=1523202090&format=interactive",
  sheets_gid="1365871671",
  sql_file="19_01.type_of_content_encoding.sql"
  )
}}

In the graph below, the top 11 content types are displayed with box sizes representing the relative number of responses. The color of each box represents how many of these resources were served compressed, orange indicates a low percentage of compression while blue indicates a high percentage of compression. Most of the media content is shaded orange, which is expected since Gzip and Brotli would have little to no benefit for them.  Most of the text content is shaded blue to indicate that they are being compressed. However, the light blue shading for some content types indicate that they are not compressed as consistently as the others.

{{ figure_markup(
  image="compression-algorithms-by-content-type-desktop.png",
  caption="Compression by type on desktop pages.",
  description="Treemap chart showing image/jpeg (91,926,198 responses - 3.27% compressed), application/javascript (80,360,676 responses - 84.88% compressed), image/png (66,351,767 responses - 3.7% compressed), text/css (54,104,482 responses - 84.0% compressed), text/html (48,670,006 responses - 44.25% compressed), image/gif (39,390,408 responses - 3.42% compressed), text/javascript (35,491,375 responses - 90.74% compressed), application/x-javascript (22,714,896 responses - 93.14% compressed), application/json (13,453,942 responses - 63.02% compressed), text/plain (4,629,644 responses - 32.89% compressed).",
  chart_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vTxUj8-0vKTqPAblIXqekSbiRh1D1lEuA3gVD9w23qwGPtJRE8FbgrURfPAgfFZX2l0t84Wy5ZAGqzR/pubchart?oid=777357707&format=interactive",
  sheets_gid="449339162",
  sql_file="19_01.type_of_content_encoding.sql"
  )
}}

Figure 19.1 above breaks down the percentage of compression used per content type, in figure 19.6 this percentage is indicated as color. The two figures tell similar stories, non-text based assets are rarely compressed, while text-based assets are often compressed. The rates of compression are also similar for both mobile and desktop.

## Первостороннее <span lang="la">vs</span> третьестороннее сжатие {первостороннее-vs-третьестороннее-сжатие}

In the [Third Parties](./third-parties) chapter, we learn about third parties and their impact on performance. Using third parties can also have an impact on compression.

<figure>
  <table>
    <thead>
      <tr>
        <td></td>
        <th scope="colgroup" colspan="2">Desktop</th>
        <th scope="colgroup" colspan="2">Mobile</th>
      </tr>
      <tr>
        <th scope="col">Content Encoding</th>
        <th scope="col">First-Party</th>
        <th scope="col">Third-Party</th>
        <th scope="col">First-Party</th>
        <th scope="col">Third-Party</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><em>No Text Compression</em></td>
        <td class="numeric">61.93%</td>
        <td class="numeric">57.81%</td>
        <td class="numeric">60.36%</td>
        <td class="numeric">58.11%</td>
      </tr>
      <tr>
        <td>Gzip</td>
        <td class="numeric">30.95%</td>
        <td class="numeric">30.66%</td>
        <td class="numeric">32.36%</td>
        <td class="numeric">30.65%</td>
      </tr>
      <tr>
        <td>br</td>
        <td class="numeric">7.09%</td>
        <td class="numeric">11.51%</td>
        <td class="numeric">7.26%</td>
        <td class="numeric">11.22%</td>
      </tr>
      <tr>
        <td>deflate</td>
        <td class="numeric">0.02%</td>
        <td class="numeric">0.01%</td>
        <td class="numeric">0.02%</td>
        <td class="numeric">0.01%</td>
      </tr>
      <tr>
        <td><em>Other / Invalid</em></td>
        <td class="numeric">0.01%</td>
        <td class="numeric">0.01%</td>
        <td class="numeric">0.01%</td>
        <td class="numeric">0.01%</td>
      </tr>
    </tbody>
  </table>
  <figcaption>{{ figure_link(caption="First-party versus third-party compression by device type.", sheets_gid="862864630", sql_file="19_03.party_of_content_encoding.sql") }}</figcaption>
</figure>

When we compare compression techniques between first and third parties, we can see that third-party content tends to be compressed more than first-party content. Additionally, the percentage of Brotli compression is higher for third-party content. This is likely due to the number of resources served from the larger third parties that typically support Brotli, such as Google and Facebook.

Compared with [last year's results](../2019/compression#first-party-vs-third-party-compression), we can see that there was a significant increase in the use of compression, notably Brotli for first parties, almost to the point that the use of compression is around 40% for both first and third party, and for desktop and mobile. However within the responses that do use compression, for first parties, the ratio of Brotli compression is only 18%, while the ratio for third parties is 27%.


## Как анализировать сжатие на вашем сайте

You can use [Firefox Developer Tools](https://developer.mozilla.org/en-US/docs/Tools) or [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) to quickly figure out what content a website already compresses. To do this, go to the Network tab, right click and activate "Content Encoding" under Response Headers. Hovering over the size of individual files you will see "transferred over network" and "resource size". Aggregated for the entire site one can see size/transferred size for Firefox and  "transferred" and "resources" for Chrome on the bottom left hand side of the Network tab.

{{ figure_markup(
  image="content-encoding.png",
  alt="How to check content encoding in DevTools",
  caption='Use DevTools to check if content encoding is used on your site',
  description="Image showing how to use DevTools to see if content encoding is used.",
  width=591,
  height=939
  )
}}

Another tool to better understand compression on your site is Google's [Lighthouse](https://developers.google.com/web/tools/lighthouse) tool, which enables you to run a series of audits against web pages. The [text compression audit](https://web.dev/uses-text-compression/) evaluates whether a site can benefit from additional text-based compression. It does this by attempting to compress resources and evaluate whether an object's size can be reduced by at least 10% and 1,400 bytes. Depending on the score, you may see a compression recommendation in the results, with a list of specific resources that could be compressed.


Because the [HTTP Archive runs Lighthouse audits](./methodology#lighthouse) for each mobile page, we can aggregate the scores across all sites to learn how much opportunity there is to compress more content. Overall, 74% of websites are passing this audit, while almost 13% of websites have scored below a 40. This is a 11.5% improvement when compared to [last year's](../2019/compression#identifying-compression-opportunities) 62.5% of passing scores.

{{ figure_markup(
  image="text-compression-lighthouse-scores.png",
  caption="Text compression Lighthouse scores.",
  description='Stacked bar chart breaking down the scores pages receive for the "enable text compression" Lighthouse audit. It shows that 7% of sites score less than 10%, 6% of sites are scoring between 10-39%, 10% of sites scoring between 40-79%, 3% of sites scoring between 80-99%, and 74% of sites have a pass with over 100% of text assets being compressed.',
  chart_url="https://docs.google.com/spreadsheets/d/e/2PACX-1vTxUj8-0vKTqPAblIXqekSbiRh1D1lEuA3gVD9w23qwGPtJRE8FbgrURfPAgfFZX2l0t84Wy5ZAGqzR/pubchart?oid=1438276663&format=interactive",
  sheets_gid="1284073179",
  sql_file="19_04.distribution_of_text_compression_lighthouse.sql"
  )
}}

## Заключение

Compared with [last year's Almanac](../2019/compression), there is a clear trend towards using more text compression. The number of responses that don't use any text compression went down a little more than 2%, while at the same time the use of Brotli has increased by almost 2%. The Lighthouse scores have improved significantly.

Text compression is widely used for the relevant formats, although there is still a significant percentage of HTTP responses that could benefit from additional compression. You can profit from taking a close look at the configuration of your server and set compression methods and levels to your need. A great impact for a more positive user experience could be made by carefully choosing defaults for the most popular HTTP servers.

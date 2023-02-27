---
layout  : wikiindex
title   : wiki
toc     : true
public  : true
comment : false
updated : 2022-12-07 23:01:03 +0900
regenerate: true
---

<div>
    <H3 class="indent">최근 변경된 문서</H3>
    <ul class="post-list">
{% assign documents = site.wiki | sort: 'updated' | reverse %}
{% for doc in documents limit: 30 %}
    {% if doc.public == true and doc.title != 'wiki'%}
        <li>
            <div class="post-item">
                <a class="post-link" href="{{ doc.url | prepend: site.baseurl }}">
                    <div class="post-meta">
                        {{ doc.updated | date: "%Y.%m.%d" }}
                        -
                        {{ doc.title }}
                    </div>
                    <div class="post-excerpt">{{ doc.summary }}</div>
                    <!-- <ul class="tag-list">
                        <li class="post-tag">
                            {{ doc. tag }}
                        </li>
                    </ul> -->
                </a>
            </div>
        </li>
    {% endif %}
{% endfor %}
    </ul>
    <h4>
        <a href="/recent/">전체 문서 리스트 보기 ({{ site.wiki | size | minus: 1 }} 항목)</a>
    </h4>
</div>
{% include createLink.html %}
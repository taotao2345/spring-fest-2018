# R2DBC

先日行った[SPRING FEST 2018](http://springfest2018.springframework.jp/)のKEYNOTEで紹介されていたのがきっかけで気になったので調べる(R2DBC自体は2018/10に行われたSpringOneというイベントで発表された)

![logo](https://avatars0.githubusercontent.com/u/40175026?s=200&v=4)

https://r2dbc.io/

# 概要

[SpringOneでR2DBCが発表された](https://www.infoq.com/jp/news/2018/10/springone-r2dbc)

翻訳されて紹介されていた、サンプルコードもあるしこれでいいな。・

元々ADBA(Asynchronous Database Access API)というプロジェクトでリレーショナルデータベースのReactiveの原則に沿った技術が開発されていた。

R2DBCの開発者も元々ADBAに参加していたが、ADBAが本来のReactiveでないと考えて(Reactiveな形を模索するために？)分離して開発したものとの事。

R2DBC自体は実験段階で、ADBAの仕様に影響を与える事が目的。なので、これ自体は実際に使えるものではない。(動作はする)

# RDB以外は？

現在はNoSQL中心にReactive Streamベースで実装されたドライバーがあり、

- Redis
- MongoDB
- Couchbase
- Cassandra

のReactive対応ライブラリがある

# 全体通して

特になし :cry:

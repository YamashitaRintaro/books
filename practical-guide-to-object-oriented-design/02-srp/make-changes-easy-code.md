### 変更が簡単なようにコードを組成する
「変更が簡単であること」の定義

- 変更は副作用をもたらさない
- 要件の変更が小さければ、コードの変更も相応して小さい
- 既存のコードは簡単に再利用できる
- もっとも簡単な変更方法はコードの追加である。ただし追加するコードはそれ自体変更が容易なものとする

コードは次のようであるべき

- 見通しが良い (Transparent) : 変更するコードにおいても、そのコードに依存する別の場所のコードにおいても、変更がもたらす影響が明白である
- 合理的 (Reasonable) : どんな変更であっても、かかるコストは変更がもたらす利益にふさわしい
- 利用性が高い (Usable) : 新しい環境、予期していなかった環境でも再利用できる
- 模範的 (Exemplary) : コードに変更を加える人が、上記の品質を自然と保つようなコードになっている

それぞれの頭文字をとってTRUE
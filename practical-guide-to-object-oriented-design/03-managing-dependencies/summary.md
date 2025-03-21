## 依存関係を理解する

以下のコード例を見てみましょう。
```ruby
class Gear
  attr_reader :chainring, :cog, :rim, :tire

  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog = cog
    @rim = rim
    @tire = tire
  end

  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end

  def ratio
    chainring / cog.to_f
  end

  # ...
end

class Wheel
  attr_reader :rim, :tire

  def initialize(rim, tire)
    @rim = rim
    @tire = tire
  end

  def diameter
    rim + (tire * 2)
  end

  # ...
end

puts Gear.new(52, 11, 26, 1.5).gear_inches # => 137.0909090909091
```

### 依存関係を認識する

上記のコードには、Gear クラスと Wheel クラスの間にいくつかの依存関係があります。具体的には次のような点です。

- 他のクラスの名前を知っている：Gear は、Wheel という名前のクラスが存在することを予想しています。
- self以外のどこかに送ろうとするメッセージの名前を知っている：Gear は、Wheel のインスタンスが diameter メソッドに応答することを予想しています。
- メッセージが要求する引数を知っている：Gear は Wheel.new に rim と tire が必要なことを知っています。
- それら引数の順番を知っている：Gear は、Wheel.new の最初の引数が rim で、2番目が tire である必要があることを知っています。
これらの依存関係により、Wheel クラスに変更が加えられた場合、Gear クラスも変更しなければならなくなる可能性があります。

オブジェクト間の結合度合い（CBO: Coupling Between Objects）が強くなると、1つのオブジェクトに変更を加えると、他の全ての関連するオブジェクトにも変更を加えなければならなくなります。このような管理されていない依存関係は、全体を修正するよりも一から書き直す方が簡単になる場合があります。

## 疎結合なコードを書く

### 依存オブジェクトの注入

依存オブジェクトの注入（Dependency Injection）は、クラス間の結合を切り離すための方法です。
これにより、クラス名やメソッド名の知識を他のクラスに持たせずに済むため、依存関係を減らすことができます。
依存オブジェクトの注入を効果的に使用するには、「クラス名やメソッド名を知っておく責任がどこか他のクラスに属するものではないか」という視点を持つことが重要です。
以下は、その例です。

```ruby
class Gear
  attr_reader :chainring, :cog, :wheel

  def initialize(chainring, cog, wheel)
    @chainring = chainring
    @cog = cog
    @wheel = wheel
  end

  def ratio
    chainring / cog.to_f
  end

  def gear_inches
    ratio * wheel.diameter
  end
end

class Wheel
  attr_reader :rim, :tire

  def initialize(rim, tire)
    @rim = rim
    @tire = tire
  end

  def diameter
    rim + (tire * 2)
  end
end

# Gear は `diameter` を知っているオブジェクトを期待している
puts Gear.new(52, 11, Wheel.new(26, 1.5)).gear_inches
```

### 依存を隔離する

依存を隔離するためには、インスタンス変数の作成を分離し、他のクラスのインスタンス作成を`initialize`メソッドから切り離して、独自に定義したメソッド内で行います。
このとき、Rubyの`||=`演算子を使って、インスタンスの作成を遅延させることが有効です。
以下はその例です。

```ruby
class Gear
  attr_reader :chainring, :cog, :rim, :tire

  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog = cog
    @rim = rim
    @tire = tire
  end

  def ratio
    chainring / cog.to_f
  end

  def gear_inches
    ratio * wheel.diameter
  end

  def wheel
    @wheel ||= Wheel.new(rim, tire)
  end
end

```

### 脆い外部メッセージを隔離する

引数の順番への依存を取り除くことも重要です。
固定された順番の引数は、その順番が変わった場合に修正箇所が膨大になります。
これを避けるために、固定順の引数をオプションハッシュに置き換えることが推奨されます。

```ruby
class Gear
  attr_reader :chainring, :cog, :wheel

  def initialize(chainring: 40, cog: 18, wheel:)
    @chainring = chainring
    @cog = cog
    @wheel = wheel
  end

  def ratio
    chainring / cog.to_f
  end

  def gear_inches
    ratio * wheel.diameter
  end
end

# chainringを指定しなかった場合、デフォルト値が使われる
puts Gear.new(wheel: Wheel.new(26, 1.5)).chainring # => 40
```

## 依存方向の管理

### 変更されないものに依存する

クラスの設計においては、「自身よりも変更されないものに依存する」という原則があります。
この原則は以下の3つの概念に基づいています。

- あるクラスは他のクラスよりも要件が変わりやすい
- 具象クラスは抽象クラスよりも変更されやすい
- 多くの場所から依存されているクラスを変更すると、広範囲に影響が及ぶ

問題となる依存関係を見つけるために、以下の質問を自問してみてください。

- このクラスは他のクラスよりも頻繁に変更されるか？
- このクラスは多くのクラスから依存されているか？
- 変更が及ぶ範囲を最小限にするためにはどうすればよいか？

これらの質問に対する答えが、依存関係を最適化するための指針となります。

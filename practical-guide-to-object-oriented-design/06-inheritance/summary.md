## 抽象的なスーパークラスを作る
オブジェクト指向の設計において、共通の振る舞いを持つクラスを抽象化することは、コードの再利用性と可読性を高めるために非常に重要です。
例えば、Bicycleクラスが共通の振る舞いを持ち、MountainBikeとRoadBikeがそれぞれ特化した振る舞いを追加する場合を考えてみましょう。

### 初期のクラス設計
以下は、初期のBicycleクラスとそのサブクラスのMountainBikeとRoadBikeの例です。

```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size

  def initialize(args={})
    @size = args[:size]
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size
  end

  def default_chain
    '10-speed'
  end

  def default_tire_size
    raise NotImplementedError, "This #{self.class} cannot respond to:"
  end
end

class RoadBike < Bicycle
  attr_reader :tape_color

  def initialize(args)
    @tape_color = args[:tape_color]
    super(args)
  end

  def default_tire_size
    '23'
  end

  def spares
    super.merge(tape_color: tape_color)
  end
end

class MountainBike < Bicycle
  attr_reader :front_shock, :rear_shock

  def initialize(args)
    @front_shock = args[:front_shock]
    @rear_shock = args[:rear_shock]
    super(args)
  end

  def default_tire_size
    '2.1'
  end

  def spares
    super.merge(front_shock: front_shock, rear_shock: rear_shock)
  end
end
```
このコードでは、Bicycleクラスが共通の属性と振る舞いを持ち、サブクラスが特化した属性や振る舞いを追加しています。

## テンプレートメソッドパターンを使う
テンプレートメソッドパターンは、スーパークラスが基本的な構造を定義し、サブクラスが具体的な実装を提供するためのパターンです。
このパターンを使うことで、共通の振る舞いをスーパークラスに集約し、サブクラスは必要な部分だけをオーバーライドできます。

```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size

  def initialize(args={})
    @size = args[:size]
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size
  end

  def default_chain
    '10-speed'
  end

  def default_tire_size
    raise NotImplementedError, "This #{self.class} cannot respond to:"
  end
end
```
このようにすることで、Bicycleクラスは基本的な構造を提供し、サブクラスが特化した部分を実装することができます。

## 具象から抽象を分ける
共通の振る舞いをスーパークラスに集約し、具体的な部分をサブクラスに分けることで、コードの再利用性と可読性が向上します。例えば、Bicycleクラスのsparesメソッドを以下のように再構成します。

```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size

  def initialize(args={})
    @size = args[:size]
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size
  end

  def default_chain
    '10-speed'
  end

  def default_tire_size
    raise NotImplementedError, "This #{self.class} cannot respond to:"
  end

  def spares
    { chain: chain, tire_size: tire_size }
  end
end

class RoadBike < Bicycle
  attr_reader :tape_color

  def initialize(args)
    @tape_color = args[:tape_color]
    super(args)
  end

  def default_tire_size
    '23'
  end

  def spares
    super.merge(tape_color: tape_color)
  end
end

class MountainBike < Bicycle
  attr_reader :front_shock, :rear_shock

  def initialize(args)
    @front_shock = args[:front_shock]
    @rear_shock = args[:rear_shock]
    super(args)
  end

  def default_tire_size
    '2.1'
  end

  def spares
    super.merge(front_shock: front_shock, rear_shock: rear_shock)
  end
end
```
このようにすることで、共通の振る舞いはBicycleクラスに集約され、特化した部分は各サブクラスが実装することになります。
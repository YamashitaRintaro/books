## ダックタイピングとは

ダックタイプは特定のクラスとも結びつかないパブリックインターフェースです。
オブジェクトが同じメソッドを持っていれば、そのクラスに関係なく同じように扱うことができます。
「もしオブジェクトがダックのように鳴き、ダックのように歩くならば、それはダックである」という考えに基づいています。

## ダックタイピングを理解する

### 重要な概念

ダックタイピングの核心は、オブジェクトが何で「ある」かではなく、「何を」するかです。
以下のコード例でその概念を見てみましょう。

```ruby
class Trip
  attr_reader :bicycles, :customers, :vehicle

  # この 'mechanic' 引数はどんなクラスのものでも良い
  def prepare(mechanic)
    mechanic.prepare_bicycles(bicycles)
  end
end

class Mechanic
  def prepare_bicycles(bicycles)
    bicycles.each { |bicycle| prepare_bicycle(bicycle) }
  end

  def prepare_bicycle(bicycle)
    # 自転車を準備するための処理
  end
end
```

ここで、Tripクラスのprepareメソッドは、引数として渡されるオブジェクトがprepare_bicyclesメソッドを持っていることに依存しています。
この例では、Mechanicクラスのインスタンスが渡されていますが、他のクラスのオブジェクトでも同じメソッドを持っていれば動作します。

## 問題を悪化させる
次に、異なるクラスを追加し、Tripクラスのprepareメソッドを変更してみます。

```ruby
class Trip
  attr_reader :bicycles, :customers, :vehicle

  def prepare(preparers)
    preparers.each do |preparer|
      case preparer
      when Mechanic
        preparer.prepare_bicycles(bicycles)
      when TripCoordinator
        preparer.buy_food(customers)
      when Driver
        preparer.gas_up(vehicle)
        preparer.fill_water_tank(vehicle)
      end
    end
  end
end

class Mechanic
  def prepare_bicycles(bicycles)
    bicycles.each { |bicycle| prepare_bicycle(bicycle) }
  end

  def prepare_bicycle(bicycle)
    # 自転車を準備するための処理
  end
end

class TripCoordinator
  def buy_food(customers)
    # 顧客のために食料を購入する処理
  end
end

class Driver
  def gas_up(vehicle)
    # 車にガソリンを入れる処理
  end

  def fill_water_tank(vehicle)
    # 車の水タンクを満たす処理
  end
end
```
このコードでは、Tripクラスのprepareメソッドが具体的なクラスに依存しています。
各クラスに対して特定のメソッドを呼び出すため、クラスに依存するコードが増え、変更が難しくなります。

## ダックを見つける
依存を取り除くために、Tripクラスのprepareメソッドを改善しましょう。
ここでは、prepareメソッドに渡される引数が「準備するもの（Preparer）」であることを意識します。

```ruby
class Trip
  attr_reader :bicycles, :customers, :vehicle

  def prepare(preparers)
    preparers.each do |preparer|
      preparer.prepare_trip(self)
    end
  end
end

# 全ての準備者 (Preparer) は 'prepare_trip' に応答するダック
class Mechanic
  def prepare_trip(trip)
    trip.bicycles.each do |bicycle|
      prepare_bicycle(bicycle)
    end
  end
end

class TripCoordinator
  def prepare_trip(trip)
    buy_food(trip.customers)
  end
end

class Driver
  def prepare_trip(trip)
    vehicle = trip.vehicle
    gas_up(vehicle)
    fill_water_tank(vehicle)
  end
end
```
このようにすることで、Tripクラスのprepareメソッドは特定のクラスに依存せず、引数として渡されるオブジェクトがprepare_tripメソッドを持っている限り動作します。

## ダックタイピングの影響
ダックタイピングを採用することで、以下のようなメリットがあります。

- 柔軟性の向上: 新しいクラスを追加する際に既存のコードを変更する必要がなくなります。
- 拡張性の向上: 新しい機能を追加する際にコードの変更が最小限で済みます。
- コードの抽象化: 具体的なクラスからより抽象的な概念に焦点を移すことができます。



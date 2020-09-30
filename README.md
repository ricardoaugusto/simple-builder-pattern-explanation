# Design Patterns: Builder

Hey dev! How's it going? Today we're going to talk about a very common situation: changes in technical requirements. Imagine this scenario:

> Client: hey, we need to build a very basic feature for our customers to build an outfit.
>
> Dev: Ok, and how should it behave? Can you please tell me some details?
>
> Client: the customer should be able to select 1 shirt, 1 trousers, 1 pair of shoes. It's that simple.
>
> Dev: Ok let me get into code! We'll talk later today.
>
> Client: Ok sounds good.

So the Dev proceeds with this little info at hand, and creates a simple Class like this:

```php
class Outfit {
    protected $shirt;
    protected $trousers;
    protected $shoes;

    public function __construct($shirt, $trousers, $shoes) {
        $this->shirt = $shirt;
        $this->trousers = $trousers;
        $this->shoes = $shoes;
    }

    public function show() {
        return $this->shirt . ' shirt with ' . $this->trousers . ' trousers and ' . $shoes . ' shoes.';
    }
}
```

Later that day, the Dev meets the Client and shows the progress:

```php
$outfit = new Outfit('Black', 'Long', 'Casual');
echo $outfit->show();
```

> Client: ok, I have a client with "Black shirt with Long trousers and Casual shoes." Seems good but we have a change in here.
>
> Client: We also need to consider the winter outfits, so let's add options for sweater, jacket, boots. And the summer outfit, and accessories. Ah! Also the gadgets, they're important.

The dev goes back to the workbench and starts incrementing the Outfit class like this:

```php
public function __construct($shirt, $trousers, $shoes, $sweater, $jacket, $boots, $sunglasses, $flops, $sandals, $and_many_more_here, $oh_boi...)
```
> Dev: ... ok that isn't going to work. Not only I can't remember the order in which the clothes are, but what if the client requests another kind? I need a better approach.
>
> Sr Dev: Why not a Builder?
>
> Dev: What's that?
>
> Sr Dev: It's a Design Pattern that allows the object creation with any number of "flavors", while avoiding constructor pollution.
>
> Dev: Can we code an example?
>
> Sr Dev: Sure, let's change your `Outfit Class` to something like this:

```php
class Outfit {
    protected $head;
    protected $torso;
    protected $legs;
    protected $feet;
    protected $accessories;

    public function __construct(OutfitBuilder $outfit) {
        $this->head = $outfit->head;
        $this->torso = $outfit->torso;
        $this->legs = $outfit->legs;
        $this->feet = $outfit->feet;
        $this->accessories = $outfit->accessories;
    }
}
```

> Sr Dev: Then we create the OutfitBuilder Class, like so:

```php
class OutfitBuilder {
    public $head = false;
    public $torso = false;
    public $legs = false;
    public $feet = false;
    public $accessories = false;

    public function head($color, $model) {
        $this->head = $color . ' hat of model ' . $model;

        return $this;
    }

    public function torso($color, $size, $model) {
        $this->torso = $color . ' ' . $model . ' of size ' . $size;

        return $this;
    }

    public function legs($color, $size, $model) {
        $this->legs = $color . ' ' . $model .  ' trousers of size ' . $size;

        return $this;
    }

    public function feet($color, $size, $model) {
        $this->head = $color . ' ' . $model . ' of size ' . $size;

        return $this;
    }

    public function accessories($items) {
        $this->head = $color . ' hat of model ' . $model;

        return $this;
    }

    public function show() {
        return new Outfit($this);
    }
}
```

> Sr Dev: so now we can use it like this:

```php
$outfit = (new OutfitBuilder())
    ->torso('Red', 'M', 'Light')
    ->legs('Black', 'M', 'Running')
    ->feet('Yellow', '10', 'Speed')
    ->show();
```

> Sr Dev: and for another client we might have:

```php
$outfit = (new OutfitBuilder())
    ->head('White', 'Casual')
    ->torso('White', 'M', 'Soft')
    ->legs('Black', 'M', 'Jeans')
    ->feet('Brown', '9', 'Sandals')
    ->show();
```

> Dev: That is much more flexible, easier to understand and maintainable. The client can come up with N number of combinations and it can handle. Thanks!

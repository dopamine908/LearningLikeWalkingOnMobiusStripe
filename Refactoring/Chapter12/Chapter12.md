# 重構 - ch12 - 處理繼承

## 提升方法(Pull Up Method)

移除重複的程式碼相當重要，如果你有一個函式，而他們重複出現在兩個甚至多個地方，那他很有可能就會變成滋養bug的溫床。
你將很有可能在某天改了其中一個，而遺忘了剩下的。

###### before

```php=

class father {}

class son1 extends father
{
    public function A();
}

class son2 extends father
{
    public function A();
}

```

###### after

```php=

class father 
{
    public function A();
}

class son1 extends father {}

class son2 extends father {}

```

## 下移方法(Push Down Method)

提升方法的逆操作。
當你的方法只與某一個子類別有關的時候，或是當需求變更，使得你在不同子類別需要不同的實作的時候。

###### before

```php=

class father 
{
    public function only_for_son1();
}

class son1 extends father {}

class son2 extends father {}

```

###### after

```php=

class father {}

class son1 extends father
{
    public function only_for_son1();
}

class son2 extends father {}

```

## 提升欄位(Pull Up Field)

將重複的欄位提升到超類別有助於減少兩種重複，第一個是可以移除重複的宣告，再者我們還有機會將子類別中會使用到這些欄位的行為移到超類別。

###### before

```php=

class father {}

class son1 extends father
{
    public $name;
}

class son2 extends father
{
    public $name;
}

```

###### after

```php=

class father 
{
    public $name;
}

class son1 extends father {}

class son2 extends father {}

```

## 下移欄位(Push Down Method)

提升欄位的逆操作。
如果某個欄位只在某個子類別會被使用，則將該欄位下移到子類別中。

###### before

```php=

class father 
{
    public $field_only_for_son1;
}

class son1 extends father {}

class son2 extends father {}

```

###### after

```php=

class father {}

class son1 extends father
{
    public $field_only_for_son1;
}

class son2 extends father {}

```

## 提升建構式內文(Pull Up Cobstructor Body)

這邊有點類似提升方法(Pull Up Method)，當多個子類別的建構子裡面有相同的過程的時候，我們將相同的過程移至父類別的建構子。

###### before

```php=

class father {}

class son1 extends father
{
    public function __construct()
    {
        //do something
    }
}

class son2 extends father
{
    public function __construct()
    {
        //do something
    }
}

```

###### after

```php=

class father 
{
    public function __construct()
    {
        //do something
    }
}

class son1 extends father {
    public function __construct(){
        parent::__construct();
    }
}

class son2 extends father {
    public function __construct(){
        parent::__construct();
    };
}

```

## 將型別代碼換成子類別(Replace Type Code with Subclasses)

子類別有兩件事情具有一些優勢
1. 如果你的程式會隨著行別代碼的值去調整行為邏輯，則子類別可以讓我們使用多型來處理行為邏輯。
2. 我們可以在建立子類別時，順便加入一些驗證邏輯，以確保欄位的值不會脫離我們的期待。

###### before

```php=

public function createEmployee(string $name, string $type)
{
    return new Employee($name, $type);
}

```

###### after

```php=

public function createEmployee(string $name, string $type)
{
    swtich ($type) {
        case 'engineer':
            return new Engineer($name);
            break;
        case 'PM':
            return new PM($name);
            break;
        default:
            return new DefaultEmployee($name)
            break;
    }
}

```

## 移除子類別(Remove Subclasses)

使用子類別代替行別代碼雖然是一個很棒的好方法，但隨著系統的演進，子類別的差異可能會在演進過程中變得很少或是沒有差異，使得你可能需要花更多的時間去理解程式，這種狀況是我們不樂見的。

###### before

```php=

class Person
{
    public function getGenderCode()
    {
        return 'x';
    }
}

class Male extends Person
{
    public function getGenderCode()
    {
        return 'M';
    }
}

class Female extends Person
{
    public function getGenderCode()
    {
        return 'F';
    }
}

```

###### after

```php=

class Persion 
{
    private $gender_code;
    
    public function getGenderCode()
    {
        return $this->gender_code;
    }
}

```

## 提取超類別(Extract Superclass)

如果有兩個類別職責上做了類似的事情，且有相似的部分，我們可以利用「繼承」來將相似的東西提取成超類別。

###### before

```php=

class Engineer
{
    private $name;
    
    public function __construct(string $name);
    
    public function getName();
    
    public function writeCode();
}

class PM
{
    private $name;
    
    public function __construct(string $name);
    
    public function getName();
    
    public function writeUserStory();
}

```

###### after

```php=

class Employee
{
    private $name;
    
    public function __construct(string $name);
    
    public function getName();
}

class Engineer extends Employee
{   
    public function __construct(string $name)
    {
        parent::__construct($name);
    }
    
    public function writeCode();
}

class PM extends Employee
{
    public function __construct(string $name)
    {
        parent::__construct($name);
    }
    
    public function writeUserStory();
}

```

## 折疊類別階層(Collapse Hierarchy)

當我們在繼承的關係裡面發現，父類別及其子類別的差異性已經小到沒有必要將他們拆開時，我們將繼承關係合併。

###### before

```php=

class Employee {...}

class Engineer extends Employee {...} 

```
###### after

```php=

class Engineer {...} 

```

## 將子類別換成委託類別(Replace Subclass with Delegate)

繼承會伴隨著一些缺點，他就像是只能打出一次的撲克牌，如果類別演變的理由不只一個，繼承本身只能選擇其中一種變異線。例如：如果我想依照人類的年齡跟收入去撰寫相符的行為，我可以使用「年情人」或是「老人」類別，貨我可以使用「富人」或「窮人」類別，但我無法同時使用這兩組。
另外一個問題是，繼承本身代表著與父類別的緊密關係，一但我修改了父類別，他會很容易造成子類別的行為被破壞。
委託可以處理這兩種問題，我可以根據不同的理由，將工作委託給許多不同的類別。委託是物件之間的一般關係，所以介面會更明確，其耦合程度會遠小於子類別化。

## 將超類別換成委託類別(Replace Superclass with Delegate)

如果超類別的功能只適用於某些子類別，而有些子類別不是用，但子類別本身的特性又可以使用超類別的所有功能，這會造成一些問題，像是這種狀況就不應該藉由繼承來使用超類別的功能。

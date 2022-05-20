#### 使用有意义并且可读的变量名称
**不好的：**
```
 String yyyymmdstr = new SimpleDateFormat("YYYY/MM/DD").format(new Date());
```


**好的：**
```
 String currentDate = new SimpleDateFormat("YYYY/MM/DD").format(new Date());
```


#### 为相同类型的变量使用相同的词汇
**不好的：**
```
getUserInfo();
getClientData();
getCustomerRecord();
```


**好的：**
```
getUser();
```


#### 使用可搜索的名称
我们要阅读的代码比要写的代码多得多，所以我们写出的代码的可读性和可搜索性是很重要的。使用没有意义的变量名将会导致我们的程序难于理解，将会伤害我们的读者，所以请使用可搜索的变量名。


**不好的：**
```
// 艹， 86400000 是什么鬼？
setTimeout(blastOff, 86400000);
```


**好的：**
```
// 将它们声明为全局常量。
public static final int MILLISECONDS_IN_A_DAY = 86400000;
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```


#### 使用解释性的变量
**不好的：**
```
String address = "One Infinite Loop, Cupertino 95014";
String cityZipCodeRegex = "/^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$/";

saveCityZipCode(address.split(cityZipCodeRegex)[0],
address.split(cityZipCodeRegex)[1]);
```


**好的：**
```
String address = "One Infinite Loop, Cupertino 95014";
String cityZipCodeRegex = "/^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$/";

String city = address.split(cityZipCodeRegex)[0];
String zipCode = address.split(cityZipCodeRegex)[1];

saveCityZipCode(city, zipCode);
```


#### 避免心理映射
显示比隐式更好


**不好的：**
```
String [] l = {"Austin", "New York", "San Francisco"};

for (int i = 0; i < l.length; i++) {
    String li = l[i];
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    // Wait, what is `$li` for again?
    dispatch(li);
}
```


**好的：**
```
String[] locations = {"Austin", "New York", "San Francisco"};

for (String location : locations) {
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    dispatch(location);
}
```


#### 不添加不必要的上下文
如果你的类名/对象名有意义，不要在变量名上再重复。


**不好的：**
```
class Car {
    public String carMake = "Honda";
    public String carModel = "Accord";
    public String carColor = "Blue";
}

void paintCar(Car car) {
    car.carColor = "Red";
}
```


**好的：**
``` 
class Car {
    public String make = "Honda";
    public String model = "Accord";
    public String color = "Blue";
}

void paintCar(Car car) {
    car.color = "Red";
}
```
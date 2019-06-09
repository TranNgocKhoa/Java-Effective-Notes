# Sử dụng Builder Pattern để khởi tạo đối tượng khi có constructer có nhiều parameter

## Các cách tạo đối tượng
Ta có class NutritionFacts:
```
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
```

Để khởi tạo đối tượng kiểu NutrionFacts, thông thường ta sẽ tạo một constructor với nhiều parameter:

```
public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
       // Gán giá trị cho biến thành viên
}

```

Nếu giả sử ta cần khởi tạo đối tượng mà chỉ cần một tập con của các biến thành viên, như vậy ta cần thêm các phiên bản constructor khác nhau.
Việc này gây ra một số bất lợi như sau:
 - Code khó đọc
 - Dễ nhầm lẫn khi các biến thành viên có cùng kiểu
 
Cách thứ 2 là sử dụng JavaBeans pattern. Với Pattern này, ta sẽ sử dụng setter thay vì các biến thể của constructor.

```
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
    
    public void setCalories(int calories) {
        this.calories = calories;
    }
    
    public void setFat(int fat) {
        this.fat = fat;
    }
    
    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }
```

Như vậy việc khởi tạo đối tượng sẽ như sau:

```
NutritionFacts nutionFacts = new NutritionFacts();
nutionFacts.setCalories(120);
nutionFacts.setServingSize(120);
nutionFacts.setServings(120);
nutionFacts.setFat(60);
nutionFacts.setSodium(23);
// ...
```

Pattern này có một số điểm yếu như sau:
- Việc khởi tạo đối tượng không được nhất quán. Vì việc khởi tạo được thực hiện thông qua nhiều hàm gọi. Trong quá trình đó có thể đối tượng sẽ bị ảnh hưởng bởi các đoạn code khác gây nên sự không nhất quán.
- Đối tượng không giữ được tính immutable cho đối tượng. Việc này sẽ không an toàn khi thao tác với đa luồng.

Các vấn đề này có thể giải quyết bằng việc sử dụng Builder Pattern.

## Builder Pattern

Builder Pattern mô phỏng named và optional parameter trong các ngôn ngữ như Python và Scala.

Code example:

Ta xây dựng một inner static class `Builder` với các biến thành viên bắt buộc được đánh dấu `final`.
Sử dụng các hàm setter với kiểu trả về là Builder.
Ở hàm `build()`, trả về đối tượng của class cần tạo.

```
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {

        // Required parameters
        private final int servingSize;
        private final int servings;

        // Optional parameters
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    public NutritionFacts(Builder builder) {
        this.servingSize = builder.servingSize;
        this.servings = builder.servings;
        this.calories = builder.calories;
        this.fat = builder.fat;
        this.sodium = builder.sodium;
        this.carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
}

```
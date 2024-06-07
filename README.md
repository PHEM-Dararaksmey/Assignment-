[](url)# Assignment CHIP MONG BANK date 6/7/2024
1.Frontend assingment
I have Ceate  2 Vue components:
1. Call AddCar for adding a car data into the backend
```vue
<template>
  <div>
    <input type="text" v-model="name" placeholder="Car name" />
    <input type="number" v-model.number="wheels" placeholder="Number of wheels" />
    <button @click="addCar">Add Car</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      name: '',
      wheels: 0
    };
  },
  methods: {
    async addCar() {
      try {
        await axios.post('http://localhost:8080/car', {
          name: this.name,
        });
        this.name = '';
        this.wheels = 0;
        alert('Car added successfully!');
      } catch (error) {
        console.error('Error adding car:', error);
        alert('Failed to add car. Please try again later.');
      }
    }
  }
};
</script>

```
2.Called Get CarInfo to Display the information on the textbong.
```vue
    <template>
  <div>
    <input type="text" v-model="carName" placeholder="Enter car name" />
    <button @click="getCarInfo">Get Car Info</button>
    <div v-if="carInfo !== null">
      <p>{{ carInfo }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      carName: '',
      carInfo: null
    };
  },
  methods: {
    async getCarInfo() {
      try {
        const response = await axios.get(`http://localhost:8080/car/${this.carName}`);
        this.carInfo = `${this.carName} has ${response.data} wheels.`;
      } catch (error) {
        console.error('Error getting car info:', error);
        alert('Failed to get car info. Please try again later.');
      }
    }
  }
};
</script>

```
make change in the App.vue 
```
<template>
  <div id="app">
    <AddCar />
    <GetCarInfo />
  </div>
</template>

<script>
import AddCar from './components/AddCar.vue';
import GetCarInfo from './components/GetCarInfo.vue';

export default {
  name: 'App',
  components: {
    AddCar,
    GetCarInfo
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

<h3> PROBLEM :
    I have a problem wiht RESTController, i can't add the data into the backend
</h3>



2. Backend assingment

Here's is my implement:
1. Create a Spring Boot Project.
2. Create a service to manage the car data
3. Create a command-line-runner to handle user interactions.
-----------------------------------------------------------
Create a class Called CarService to manage the car data, storing it in a 'HashMap'

``` java
package com.example.carinfo;

import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

@Service
public class CarService {
    private final Map<String, Integer> carData = new HashMap<>();

    public boolean containsCar(String carName) {
        return carData.containsKey(carName);
    }

    public void addCar(String carName, int wheels) {
        carData.put(carName, wheels);
    }

    public int getWheels(String carName) {
        return carData.get(carName);
    }

    public List<String> getCarsByWheels(int wheels) {
        return carData.entrySet().stream()
                .filter(entry -> entry.getValue() == wheels)
                .map(Map.Entry::getKey)
                .collect(Collectors.toList());
    }

    public List<String> getAllCars() {
        return carData.keySet().stream().collect(Collectors.toList());
    }
}
```
Create the interface called CarInfoApplication and Implement CommandLine Runner to execute the code when the Spring Boot application start in console.

```java
  package com.example.carinfo;

import com.example.carinfo.CarService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import java.util.List;
import java.util.Scanner;

@SpringBootApplication
public class CarInfoApplication implements CommandLineRunner {

    @Autowired
    private CarService carService;

    public static void main(String[] args) {
        SpringApplication.run(CarInfoApplication.class, args);
    }

    @Override
    public void run(String... args) {
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.print("Car name? ");
            String carName = scanner.nextLine();

            if (carName.equalsIgnoreCase("Bye")) {
                System.out.println("Goodbye!");
                break;
            } else if (carName.equalsIgnoreCase("4-wheel-car")) {
                List<String> cars = carService.getCarsByWheels(4);
                System.out.println("[" + cars.size() + "] " + String.join(", ", cars));
            } else if (carName.equalsIgnoreCase("2-wheel-car")) {
                List<String> cars = carService.getCarsByWheels(2);
                System.out.println("[" + cars.size() + "] " + String.join(", ", cars));
            } else if (carName.equalsIgnoreCase("All")) {
                List<String> cars = carService.getAllCars();
                System.out.println("[" + cars.size() + "] " + String.join(", ", cars));
            } else {
                if (carService.containsCar(carName)) {
                    int wheels = carService.getWheels(carName);
                    System.out.println("\"" + carName + "\" has " + wheels + " wheels.");
                } else {
                    System.out.print("\"" + carName + "\" is not on my list. Number of wheels? ");
                    int wheels = Integer.parseInt(scanner.nextLine());
                    carService.addCar(carName, wheels);
                    System.out.println("Thanks. I updated the information.");
                }
            }
        }
        scanner.close();
    }
}

```


II Write SQL statment 
1. Count each branch how many accounts do they have in  CUST_ACCOUNT_INFO Table 
```SQL
    SELECT Branch_Code, COUNT(*) AS Number_of_Accounts
    FROM CUST_ACCOUNT_INFO
    GROUP BY Branch_Code;
```
2. who have more than one account in CUST_ACCOUNT_INFO Table.
```SQL
    SELECT Customer No
    FROM CUST_ACCOUNT_INFO
    GROUP BY Customer No
    HAVING COUNT(*) > 1;
 ```
3.Find customers who don’t have account yet?
Identify customers present in CUSTOMER_INFO but not in CUST_ACCOUNT_INFO, indicating they don't have accounts yet.
```SQL
    SELECT c.Customer_No, c.Customer_Name
    FROM CUSTOMER_INFO AS c
    LEFT JOIN CUST_ACCOUNT_INFO AS a ON c.Customer_No = a.Customer_No
    WHERE a.Customer_No IS NULL;
 ```

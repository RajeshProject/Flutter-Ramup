# Complete Flutter details

## Navigation - MaterialPageRoute

- Flutter manages navigating from one screen to another screen.
- There is many types of navigation

1. **PUSH**

```dart
Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => const SecondRoute()),
  );
```

2. **PUSH WITH DATA**

- It can be done by passing it as parameter

- Screen 1 - While calling the function pass the data as parameter

```dart
Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => Screen2(
                    name:"Rajesh",
                    id:"12345",
                  ),
                ),
              );
```

- Screen 2 - Initalize the arguments in constructor to access data.

```dart
class Screen2 extends StatelessWidget {
  final String name;
  final String id;

  Screen2({required this.name, required this.id});
```

3. **POP**

```dart
Navigator.pop(context);
```

> Navigation to using arrow symbol **"<-"** at top and Noraml mobile **back** button both perform **pop method**.


4. **POP WITH DATA**

 - Step 1:

 Pass any type of data from pop method

 ```dart
 Navigator.pop(context, "Data from pop");
 ```

 - Step 2:

 Get the data after pop by using async and await

 ```dart
 onPressed: () async {
  final data = await Navigator.push(
    context,
    MaterialPageRoute(
      builder: (context) => Screen2(
        name: "Rajesh",
        id: "12345",
      ),
    ),
  );
  print(data);
},
 ```

 ## Navigation - Named Route

- We can do navigation with name.
- There is some main methods in using Named Route


> **Note:**
>
> 1. If you use "initRoute" you should not use "home" property.
> 2. You should initialze the Route Name inside routes property. If you didn't and push inside the navigate, it will throw an error.


**1. Navigate to pages**

> - Step 1:
>
> Create Static Id for the Screens
> ```dart
> class HomePage extends StatelessWidget {
>  static String id = "HomePage";
> ```
>
> - Step 2 :
>
> Initialize "initRoute" property under MaterialApp
>
> ```dart
> MaterialApp(
>     initialRoute: HomePage.id,
> ```
>
> - Step 3 :
>
> Register all the page routes in "routes" property
> 
>```dart
>routes: {
>        HomePage.id: ((context) => const HomePage()),
>        LoginPage.id: ((context) => const LoginPage()),
>        ProfilePage.id: ((context) => const ProfilePage()),
>      },
>```
>
> - Step 4 :
> Use ".pushNamed" method to move to another page
>
> ```dart
> Navigator.pushNamed(context, LoginPage.id);
> ```
>
> - Step 5 :
> Use ".pop" method to move back page
>
> ```dart
> Navigator.pop(context);
> ```
>

**2. Pass data using arguments**

- **METHOD 1**

> - Step 1:
> Create argument model.
> ```dart
>class LoginArguments {
>  String userName;
>  String password;
>  LoginArguments({required this.userName, required this.password});
>}
>```
>
> - Step 2:
> Pass the data in "arguments" property as model.
> ```dart
>Navigator.pushNamed(
>   context,
>   ProfilePage.id,
>   arguments: LoginArguments(userName: "Rajesh", password: "Rajeshgn"),
> );
> ```
>
> - Step 3:
> Access the argument data using **"ModalRoute"**.
> ```dart
>late LoginArguments args = ModalRoute.of(context)!.settings.arguments as LoginArguments;
> ...
> 'Hi ${args.userName}'
>```
>
>

**Properties**

1. ".pushNamedAndRemoveUntil" - method used to remove the routes from the stack.

> Eg:
> If you logged in and moved into many page and if you logout it should remove all the routes from the page and navigate to login page
> 
> ```dart
> Navigator.pushNamedAndRemoveUntil(context, '/login',Route<dynamic> route) => false);
> ```
> ![image](https://user-images.githubusercontent.com/110470373/185064456-2060d0e6-51e3-4844-bbcb-452e3e883ce1.png)
>
> Above method will remove all the routes and add ".login" route inside the stack. Also we can use another method to specify removing untill the screen.
>
> ```dart
> Navigator.pushNamedAndRemoveUntil(context, '/calendar', ModalRoute.withName('/'));
>```
>
> ![image](https://user-images.githubusercontent.com/110470373/185064999-95140173-4fd1-4f30-84c6-4fb3f6c565d4.png)
>




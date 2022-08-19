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

- **Method 2**

> We can use **onGenerateRoute**
>```dart
>MaterialApp(
>  onGenerateRoute: (settings) {
>    if (settings.name == PassArgumentsScreen.routeName) {
>      final args = settings.arguments as ScreenArguments;
>      return MaterialPageRoute(
>        builder: (context) {
>          return PassArgumentsScreen(
>            title: args.title,
>            message: args.message,
>          );
>        },
>      );
>    }
>    return null;
>  },
>)
>```

**Properties**

1. ".pushNamedAndRemoveUntil" - method used to remove the routes from the stack.

> Eg:
> If you logged in and moved into many page and if you logout it should remove all the routes from the page and navigate to login page
> 
> ```dart
> Navigator.pushNamedAndRemoveUntil(context, '/login',(Route<dynamic> route) => false);
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

2. ".popUntil"

> ```dart
> void _logout() {
>   Navigator.popUntil(context, ModalRoute.withName('/login'));
> }
> ```



## Builder & BuildContext

**Used to answer the questions**

- where am i located in tree
- what going on around me

> Note:
>
> - The contaxt can get information about the parent widgets not of its child widget.
> - It will check the previous widget in vertically not in horizontally.

Eg :
Direct accessing context of scaffold which created an issue 
![image](https://user-images.githubusercontent.com/110470373/185095684-54050c51-42eb-401d-9a15-dccd20a9b174.png)

Add Builder to solve the problem

![image](https://user-images.githubusercontent.com/110470373/185095952-3e77d838-8d72-4725-b32d-f463495426b0.png)



## Future builder

- Used to build widget when future is involved.

Eg: Assume there is method with single future return value

```dart
Future<int> getData() async {
  await Future.delayed(Duration(seconds: 4));
  return 5;
}
```

We can use Future builder to interact with this future method

```dart
 FutureBuilder(
  future: getData(),
  initialData: 2,
  builder: (context, snapshot) {
    switch (snapshot.connectionState) {
      case ConnectionState.waiting:
        return Text("Waiting");
      case ConnectionState.done:
      default:
        if (snapshot.hasError) {
          return Text("Failed");
        } else if (snapshot.hasData) {
          int data = snapshot.data! as int;
          return Text("${data}");
        } else {
          return Text("Loading..");
        }
    }
  },
),
```

So that we can access the data, check the status and many more. To know more [Future Builder](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html).


## Stream Builder

- In Future builder the method return one single time the value but if we have multiple return with many delay we can go for stream.

```dart
Stream<int> getData() async* {
  await Future.delayed(const Duration(seconds: 3));
  yield 1;

  await Future.delayed(const Duration(seconds: 1));
  yield 2;

  await Future.delayed(const Duration(seconds: 1));
  yield 3;
}
```

```dart
StreamBuilder(
  stream: getData(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return Text("Loading");
    } else if (snapshot.hasError) {
      return Text("${snapshot.error}");
    } else if (snapshot.hasData) {
      return Text("${snapshot.data}");
    } else {
      return Text("No Data");
    }
  },
),
```

> Note: To have full control over the stream, you can use all the connectionState
>
> ```dart
> switch(snapshot.connectionState){
>  case ConnectionState.none: // Stream is null 
>  case ConnectionState.waiting: // Waiting untill we get first value from the stream
>  case ConnectionState.active: // Got the first value and not yet finished
>  case ConnectionState.done: // If the stream is finished
>  default:
>    return Container();
>}
>```

# Johannes 

## ListView with json Data

```dart
static Future<List<User>> getUsers() async {
  await Future.delayed(const Duration(seconds: 3));
  const data = [
    {"userName": "Rajesh", "email": "rajesheffivitytest@gmail.com"},
    {"userName": "Mahesh", "email": "mahesheffivitytest@gmail.com"},
    {"userName": "Sathya", "email": "sathyaeffivitytest@gmail.com"},
  ];
  return data.map<User>(User.fromJson).toList();
}
```


```dart
FutureBuilder<List<User>>(
  future: usersFuture,
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return const Center(child: CircularProgressIndicator());
    } else if (snapshot.hasError) {
      return Text("${snapshot.error}");
    } else if (snapshot.hasData) {
      final users = snapshot.data!;
      return buildUsers(users);
    } else {
      return Text("No data found");
    }
  },
),
```

```dart
class User {
  final String userName;
  final String email;
  User({required this.email, required this.userName});

  // Takes Json and converts to user object
  static User fromJson(json) => User(
        userName: json["userName"],
        email: json["email"],
      );
}

```

## Flutter Vertical List View - Scrollable

- We can use ***"SingleChildListView"*** to make all the childrens scrollable.
- Else We can use ***"ListView"*** to make all the childrens srollable
- **Note: But it renders all the childrens if it not visible, SO we can use ListView.builder**

```dart
 ListView.builder(
  itemCount: 100,
  itemBuilder: ((context, index) {
    return getCard(index);
  }),
),
```


## Flutter Horizontal List View - Scrollable

- We can use ***"SingleChildListView"*** to make all the childrens scrollable.
- Else We can use ***"ListView"*** to make all the childrens srollable
- **Note: But it renders all the childrens if it not visible, SO we can use ListView.builder**

```dart
 ListView.builder(
  scrollDirection: Axis.horizontal,
  itemCount: 100,
  itemBuilder: ((context, index) {
    return getCard(index);
  }),
),
```

> **Note:** You can use seperated method to give spaces between cards/Widgets !
>
> ```dart
>  ListView.separated(
> scrollDirection: Axis.horizontal,
> itemCount: 100,
> separatorBuilder: (context, index) {
>   return const SizedBox(
>     width: 10.0,
>   );
> },
> itemBuilder: ((context, index) {
>     return getCard(index);
>   }),
> ```


## Null Safety

```dart
String? variable=null;

void main(){
  // ?
  print(variable?.toLowerCase()); // null
  print(variable?.length); // null
  // !
  print(variable!.toLowerCase()); // throws exception // null + ! = crash
  print(variable!.length); // throws exception
  String var1= variable!; // throws exception
  // ??
  String var2= variable ?? 'Default';
  //late 
      // Giving promise to dart that it will be initialize later
      // If you didn't it will throw an error
  // ??=
  variable ??="One";
  print(variable);
}
```

## Sized Box

- Used to give space between widget because it contains width and height property.
- If you rap around any widget with width and height it make the child to have his width and height.

```dart
SizedBox(
  width:100.0,
  height:100.0,
  child:Container(
    width:300.0,
    height:300.0,
  ),
)
```

- SizedBox has a property "SizedBox.expand" which will take whole width and height

```dart
SizedBox.expand(
  child:Container(
    width:300.0,
    height:300.0,
  ),
)
```



## Spread Operator (...)

- If you want to insert items at middle in a list, you can use spread operator.

```dart

final items = const [Text('Item 2'),Text('Item 3')]
.
.
.

Column(
  children:[
    Text('Item 1'),
    Column(children:items), // method 1
    Text('Item 4'),
  ]
)

Column(
  children:[
    Text('Item 1'),
    ...items, // method 2 // Spread operator
    Text('Item 4'),
  ]
)
```

```dart
main() {
    
  // Creating lists
  List list1 = ['Mango', 'Apple'];
  List list2 = ['Orange', 'Avocado', 'Grape'];
  List list3 = ['Lemon'];
    
  // Combining lists
  var combinedList = [...list1, ...list2, ...list3];
    
  // Printing combined list
  print(combinedList);

  // extending newList using spread operator
  List newList = ['One',...list1];
  // display result
  print(newList);

}

```

## Collection If and Collection For

#### If

```dart
  children:[
    Text('Item 1'),
    if(true) // collection If
      Text('Item 2'),
  ]

```

```dart
  children:[
    Text('Item 1'),
    for(int i=2; i< 10; i++) // collection For
      Text('Item $i'), // can be accessable
    Text('Not working $i'),  // cannot be accessable
  ]
```

```dart
final items = ['Item 2','Item 3'];
.
.
.
children:[
  Text('Item 1'),
  for(final item in items) // collection For
    Text(item), 
]
```

## Reorderable List

- We can Drag and Drop item in list view.

```dart
 ReorderableListView(
  onReorder: (oldIndex, newIndex) {
    setState(() {
      if (newIndex > oldIndex) newIndex--;
      final item = items.removeAt(oldIndex);
      items.insert(newIndex, item);
    });
  },
  children: [
    for (final item in items)
      ListTile(
        key: ValueKey(item), // When you use Reorderable give key value, to identify each item !
        title: Text(item),
      ),
  ],
),
```


## DropDown in AppBar

- We can use "PopupMenuButton" widget.

```dart
PopupMenuButton(
  onSelected: (value) => {
    if (value == MenuItem.item1)
      {
        // clicked "item 1"
      }
  },
  itemBuilder: (context) => [
    const PopupMenuItem(
      value: MenuItem.item1,
      child: Text("Item1"),
    ),
    const PopupMenuItem(
      value: MenuItem.item2,
      child: Text("Item2"),
    ),
    const PopupMenuItem(
      value: MenuItem.item3,
      child: Text("Item3"),
    ),
    const PopupMenuItem(
      value: MenuItem.item4,
      child: Text("Item4"),
    ),
  ],
),
```

- Use "Drawer" Widget to make side menu.

```dart
return Scaffold(
drawer: getMenu(),
.
.
.
Widget getMenu() {
    return Drawer(
      child: Container(
        child: ListView(
          children: [
            for (int i = 1; i <= 10; i++)
              ListTile(
                title: Text("Item $i"),
              ),
            Divider(
              color: Colors.black54,
            )
          ],
        ),
      ),
    );
  }
```

## FittedBox 

- Fit widget into the available space of the another widget.
- It will make the child size to fit into the parent widget size.
- To make the child widget size to small size use "fit:Boxfit.scaleDown".

```dart
 Container(
  color: Colors.green,
  height: 300,
  width: 200,
  child: FittedBox(
    child: Center(
      child: Text(
        'Flutter',
        style: TextStyle(
          fontSize: 64, // Ignored
        ),
      ),
    ),
  ),
);
```

![image](https://user-images.githubusercontent.com/110470373/185541231-d31d018a-60c5-4c2b-b7d6-50e5905ed2fa.png)


## PageView and Page Controller

- "PageView" used to make swipe pages horizontally or vertically.

```dart
PageView(
  scrollDirection: Axis.vertical,
  onPageChanged: (value) {
    print("Page ${value + 1}");
  },
  children: [
    Container(
      color: Colors.red,
      child: const Center(
        child: Text(
          "Page1",
          style: TextStyle(fontSize: 20.0, color: Colors.white),
        ),
      ),
    ),
    Container(...),
    Container(...),
  ],
),
```

- You can use controller to control the page view like initalize the default page to show, 


```dart
  final controller = PageController(initialPage: 1); // Initialize the page to show by default
  .
  .
  .
  PageView(
    scrollDirection: Axis.vertical,
    controller: controller,
  .
  .
  .              
  controller.animateToPage(0, duration: const Duration(seconds: 1), curve: Curves.easeInOut); // set the page it should move
  controller.previousPage(duration: const Duration(seconds: 1), curve: Curves.easeInOut);
  controller.nextPage(duration: const Duration(seconds: 1), curve: Curves.easeInOut);


```

## Expansion Panel

- Used to create expand and collapse list items in the list.

![image](https://user-images.githubusercontent.com/110470373/185564848-6cc37fb2-ff29-485d-b1dd-cc66d3625a48.png)

1. We can use "ExpansionPanelList.radio" and "ExpansionPanelRadio" to create expand and collapse. But We can make only one panel to active. To set multiple panel active follow method 2.

```dart
ExpansionPanelList.radio(
  children: items
      .map((item) => ExpansionPanelRadio(
            canTapOnHeader: true,
            value: item.header,
            headerBuilder: (context, isExpanded) => ListTile(
              title: Text(item.header),
            ),
            body: ListTile(
              title: Text(item.body),
            ),
          ))
      .toList(),
),
```

2. We can use "ExpansionPanelList" and "ExpansionPanel" to achive multiple panel active.

```dart
ExpansionPanelList(
  expansionCallback: ((panelIndex, isExpanded) {
    setState(() {
      items1[panelIndex].isExpanded = !isExpanded;
    });
  }),
  children: items1
      .map((item) => ExpansionPanel(
            isExpanded: item.isExpanded,
            backgroundColor: Colors.orange.shade100,
            canTapOnHeader: true,
            headerBuilder: (context, isExpanded) => ListTile(
              title: Text(item.header),
            ),
            body: ListTile(
              title: Text(item.body),
            ),
          ))
      .toList(),
),
```

## Search And Filter

- To show how use filter list view using search.
- Each time the value is change in the input then filter the list book and updated in listview.

```dart
void getFilterBooks(String query) {
    final suggestions = allBooks.where((element) {
      final booltitle = element.title.toLowerCase();
      final input = query.toLowerCase();
      return booltitle.contains(input);
    }).toList();
    setState(() {
      books = suggestions;
    });
  }
```

## Intrinsic Height & Intrinsic Width Widgets

- To apply same height to widgets.

1. Intrinsic Height

![image](https://user-images.githubusercontent.com/110470373/185582615-b4e3b6b1-143d-450a-b990-c49c27ed2518.png)

![image](https://user-images.githubusercontent.com/110470373/185582757-61aef217-607e-4b55-b19f-12e317d22342.png)

![image](https://user-images.githubusercontent.com/110470373/185582215-522a2ed9-338b-4f66-8ee9-0d2de59776cf.png)

2. Intrinsic Width

![image](https://user-images.githubusercontent.com/110470373/185582947-98016270-39c4-4cc7-9e10-67cd846ae3b8.png)

![image](https://user-images.githubusercontent.com/110470373/185583271-e60aed05-6c2e-4d6d-b011-68bf99fc84c8.png)


## Login Form

- Create form of email and password with validation.

1. **Create a Form with a GlobalKey**

- The Form widget acts as a container for grouping and validating multiple form fields.

```dart

  final _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: <Widget>[
        ],
      ),
    );
  }
}
```

2. **Add a TextFormField with validation logic**

```dart
TextFormField(
  validator: (value) {
    if (value == null || value.isEmpty) {
      return 'Email is invalid';
    }
    return null;
  },
),
```

3. **Create a button to validate and submit the form**

```dart
 ElevatedButton(
  onPressed: () {
    final isFormValid = formKey.currentState!.validate(); //currentState- method to access the FormState,
    if (isFormValid) {}
  },
  child: Text("Submit"),
)
```

## DropDown Menu

```dart
 DropdownButtonFormField<String>(
  items: items.map((item) => DropdownMenuItem(value: item, child: Text(item))).toList(),
  value: selectedItem,
  onChanged: ((value) {
    setState(() {
      selectedItem = value;
    });
  }),
),
```

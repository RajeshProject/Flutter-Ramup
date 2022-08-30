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

## WillPopScope widget.

- Used to handle back button pressed in flutter.
- Need to wrap scaffold with "WillPopScope" which has a property "onWillPop" callback method.
- If you return false in the function, it will stop going back in app.

```dart
 WillPopScope(
      onWillPop: () async {
        return true;
      },
      child: Scaffold(),
    );
```

## Gesture Detector & InkWell Widget

1. **Gesture Detector**

- It used to detect events.
- If you wrap any widget inside Gesture, You can track many widgets like single tab, double tab, long press etc..,

```dart
 GestureDetector(
onTap: () => setState(() => text = "Single Tab"),
onDoubleTap: () => setState(() => text = "Double Tab"),
onLongPress: () => setState(() => text = "Long Press"),
child:Container()
)
```


2. **InkWell**

- It used to detect events with splash effect.
- If you wrap any widget inside Inkwell with the child as "Ink" Widget, you can get splash effect.
- You can customise the splash colors as well.
- Use **"ClipRRect"** widget to make the box rounded. Importantly put **Material** Widget between ClipRRext and Inkwell widgets.
- Use **"ClipOval"** widget to make box circle.

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(10.0),
  child: Material(
    child: InkWell(
      highlightColor: Colors.orange.withOpacity(0.3),
      splashColor: Colors.red.shade100,
      onTap: () => setState(() => text = "Single Tab"),
      onDoubleTap: () => setState(() => text = "Double Tab"),
      onLongPress: () => setState(() => text = "Long Press"),
      child: Ink(
        color: Colors.blue,
        width: size.width * 0.5,
        height: size.height * 0.3,
        child: Center(
          child: Text(
            text,
            style: TextStyle(fontSize: 20.0, color: Colors.white),
          ),
        ),
      ),
    ),
  ),
),
```


## CheckBox & CheckBoxListTile

```dart
Checkbox(
  value: isChecked,
  onChanged: (value) {
    setState(() {
      isChecked = value!;
    });
  },
),
```

```dart
CheckboxListTile(
  controlAffinity: ListTileControlAffinity.leading, // Make checkbox to be leading
  value: isChecked1,
  title: Text("This is to check Checkbox"),
  subtitle: Text("It working fine"),
  onChanged: (value) {
    setState(() {
      isChecked1 = value!;
    });
  },
)
```

## TabBar

1. Wrap Scaffold inside "DefaultTabController" and set lenght.

```dart
DefaultTabController(
      length: 3, // No of taps
      child: Scaffold(
      ),
    );
  }
```

2. Add Tabs in appbar with icon (optional)

```dart
appBar: AppBar(
title: const Text("TapBar"),
bottom: const TabBar(tabs: [
  Tab(
    text: 'Tab 1',
    icon: Icon(Icons.home),
  ),
  Tab(
    text: 'Tab 2',
    icon: Icon(Icons.star),
  ),
  Tab(
    text: 'Tab 3',
    icon: Icon(Icons.person),
  )
])),
```

3. Create Tabs in body.

```dart
body: const TabBarView(children: [
Center(
  child: Text(
    "Page 1",
    style: TextStyle(fontSize: 20.0),
  ),
),
Center(
  child: Text(
    "Page 2",
    style: TextStyle(fontSize: 20.0),
  ),
),
Center(
  child: Text(
    "Page 3",
    style: TextStyle(fontSize: 20.0),
  ),
)
,
```


## Navigation Bar

1. Create "NavigationBar" in scaffold and make it to detect the tabs. 
2. creates pages and using the index values make the pages to show in body.

```dart
int index = 0;
  final pages = [
    Center(child: Text("Page 1", style: TextStyle(fontSize: 20.0))),
    Center(child: Text("Page 2", style: TextStyle(fontSize: 20.0))),
    Center(child: Text("Page 3", style: TextStyle(fontSize: 20.0)))
  ];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: pages[index],
      bottomNavigationBar: NavigationBar(
        onDestinationSelected: (value) => setState(() => index = value),
        selectedIndex: index,
        destinations: const [
          NavigationDestination(
            icon: Icon(Icons.email_rounded),
            label: 'page 1',
            selectedIcon: Icon(Icons.email),
          ),
          NavigationDestination(
            icon: Icon(Icons.chat_bubble_outline),
            label: 'page 2',
            selectedIcon: Icon(Icons.chat_bubble),
          ),
          NavigationDestination(
            icon: Icon(Icons.videocam_outlined),
            label: 'page 3',
            selectedIcon: Icon(Icons.videocam),
          ),
        ],
      ),
    );
  }
```

## Expansion Tile

- Used to create a expanding dropdown with options.

```dart
ExpansionTile(
  title: Text('Item', style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
  children: [
    ListTile(title: Text("Item 1", style: TextStyle(fontSize: 15))),
    ListTile(title: Text("Item 2", style: TextStyle(fontSize: 15))),
    ListTile(title: Text("Item 3", style: TextStyle(fontSize: 15))),
    ListTile(title: Text("Item 4", style: TextStyle(fontSize: 15))),
    ListTile(title: Text("Item 5", style: TextStyle(fontSize: 15))),
  ],
);
```


## Pop-up Dialog Widget

- We can create pop-up in a page using "AlertDialog" widget.

```dart
AlertDialog(  
    title: Text("Simple Alert"),  
    content: Text("This is an alert message."),  
    actions: [  
      okButton,  
    ],  
  );  
```

![image](https://user-images.githubusercontent.com/110470373/185730754-9fc248bb-2208-4271-abf0-d35413409c51.png)


## Table

- Create table with row and headers.

```dart
Widget build(BuildContext context) {
    return Center(
      child: Table(
        border: TableBorder.all(),
        columnWidths: {
          0: FractionColumnWidth(0.5), //50%
          1: FractionColumnWidth(0.25), //25%
          2: FractionColumnWidth(0.25) //25%
        },
        children: [
          buildRow(["Name", "City", "Age"], isHeader: true),
          buildRow(["Sarah", "New York", "21"]),
          buildRow(["Emma", "Sydney", "23"]),
          buildRow(["John", "London", "33"])
        ],
      ),
    );
  }

  TableRow buildRow(List<String> cells, {bool isHeader = false}) => TableRow(
          children: cells.map((text) {
        TextStyle textStyle = TextStyle(fontSize: 12, fontWeight: isHeader ? FontWeight.bold : FontWeight.normal);
        return Padding(
          padding: const EdgeInsets.all(10.0),
          child: Center(
              child: Text(
            text,
            style: textStyle,
          )),
        );
      }).toList());
```


## DateRange Picker

- select range of date in flutter using "dateRangePicker".


```dart
DateTimeRange dateRange = DateTimeRange(
  start: DateTime(2022, 08, 01), 
  end: DateTime(2022, 08, 20)
  );

  @override
  Widget build(BuildContext context) {
    final start = dateRange.start;
    final end = dateRange.end;
    final difference = dateRange.duration;
    return Column(
      children: [
        Container(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              ElevatedButton(
                onPressed: () {
                  getDateRange();
                },
                child: Text(DateFormat('yyyy/MM/dd').format(start)), // package : intl : ^0.17.0
              ),
              const SizedBox(
                width: 10.0,
              ),
              ElevatedButton(
                onPressed: () {
                  getDateRange();
                },
                child: Text(DateFormat('yyyy/MM/dd').format(end)),
              ),
            ],
          ),
        ),
        Center(
          child: Text("Difference is ${difference.inDays} days"),
        )
      ],
    );
  }

  Future getDateRange() async {
    DateTimeRange? result = await showDateRangePicker(
      context: context,
      initialDateRange: dateRange,
      firstDate: DateTime(1900), // Define min date
      lastDate: DateTime(2100), // Define max date
    );
    if (result == null) return; // pressed 'X'

    setState(() {
      dateRange = result;
    });
  }
```


## Date And Time

```dart
DateTime defaultDate = DateTime(2022, 08, 20, 5, 30);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              ElevatedButton(
                onPressed: () {
                  pickDate();
                },
                child: Text(DateFormat('yyyy/MM/dd').format(defaultDate)), // package : intl : ^0.17.0
              ),
              const SizedBox(
                width: 10.0,
              ),
              ElevatedButton(
                onPressed: () {
                  pickTime();
                },
                child: Text(DateFormat('HH:mm').format(defaultDate)), // package : intl : ^0.17.0
              ),
            ],
          ),
        ),
        Container(
          child: Column(children: [
            SizedBox(
              width: 190,
              child: ElevatedButton(
                onPressed: () {
                  pickDateTime();
                },
                child: Text(DateFormat('yyyy/MM/dd HH:mm').format(defaultDate)), // package : intl : ^0.17.0
              ),
            ),
          ]),
        )
      ],
    );
  }
}
```

```dart

  Future pickDate() async {
    DateTime? result = await showDatePicker(context: context, initialDate: defaultDate, firstDate: DateTime(1900), lastDate: DateTime(2100));
    if (result == null) return;
    setState(() {
      final newDateTime = DateTime(result.year, result.month, result.day, defaultDate.hour, defaultDate.minute);
      defaultDate = newDateTime;
    });
  }

  Future pickTime() async {
    TimeOfDay? result = await showTimePicker(context: context, initialTime: TimeOfDay(hour: defaultDate.hour, minute: defaultDate.minute));
    if (result == null) return;
    final newDateTime = DateTime(defaultDate.year, defaultDate.month, defaultDate.day, result.hour, result.minute);
    setState(() {
      defaultDate = newDateTime;
    });
  }

  Future pickDateTime() async {
    DateTime? date = await showDatePicker(context: context, initialDate: defaultDate, firstDate: DateTime(1900), lastDate: DateTime(2100));
    if (date == null) return;
    TimeOfDay? time = await showTimePicker(context: context, initialTime: TimeOfDay(hour: defaultDate.hour, minute: defaultDate.minute));
    if (time == null) return;
    final newDateTime = DateTime(date.year, date.month, date.day, time.hour, time.minute);
    setState(() {
      defaultDate = newDateTime;
    });
  ```

  ## Disable Button

  ```dart
  class _JDiableButtonState extends State<JDiableButton> {
  bool isButtonActive = false;
  late TextEditingController controller;
  
  @override
  void initState() {
    super.initState();
    controller = TextEditingController();
    controller.addListener(() {
      final isButtonActive = controller.text.isNotEmpty;
      setState(() {
        this.isButtonActive = isButtonActive;
      });
    });
  }

  @override
  void dispose() {
    super.dispose();
    controller.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      width: 180,
      child: Column(
        children: [
          TextField(
            decoration: InputDecoration(label: Text('Name')),
            controller: controller,
          ),
          ElevatedButton(
            onPressed: isButtonActive
                ? () {
                    controller.clear();
                  }
                : null,
            child: const Text("Clear"),
          )
        ],
      ),
    );
  }
}
  ```

```dart
onChanged: (value) {
  setState(() {
    isButtonActive = value.isNotEmpty;
  });
},
```

## ListView vs GridView

- Create "ListView" using builder

```dart
ListView.builder(
itemCount: items.length,
itemBuilder: (context, index) {
  final item = items[index];
  return ListTile(
    leading: CircleAvatar(radius: 28, backgroundImage: NetworkImage("https://source.unsplash.com/random?sig=$index")),
    subtitle: Text("Subtitle $index"),
    title: Text(item),
    onTap: () => selectItem(item),
  );
},

```

- Create "GridView" using GridView.builder.

```dart
GridView.builder(
gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2), // To decide no of items in row
itemCount: items.length,
itemBuilder: ((context, index) {
  final item = items[index];
  return GridTile(
    child: InkWell(
      onTap: () => selectItem(item),
      child: Ink.image(
        image: NetworkImage("https://source.unsplash.com/random?sig=$index"),
        fit: BoxFit.cover,
      ),
    ),
    footer: Text(
      item,
      style: TextStyle(fontSize: 32, color: Colors.white, fontWeight: FontWeight.bold),
    ),
  );
}))
```

- Combine both builder using a flag to set the view type.

```dart
bool isGrid = true;
.
.
.
isGrid ? GridView.builder(...) : ListView.builder(...)
.
.
.
void selectItem(String item) { // clicking the items to show snackBar
    final snackBar = SnackBar(
      content: Text(
        "Hello $item !!",
        style: TextStyle(fontSize: 15),
      ),
      backgroundColor: Colors.blue,
    );
    ScaffoldMessenger.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(snackBar);
  }
```

## Change Button Color on Tap

- Change BackGround color of button when it pressed using "overlayColor".

```dart
 ElevatedButton(
        style: ButtonStyle(overlayColor: getColor(Colors.white, Colors.teal)), // 1- normal color, 2- pressed color
        onPressed: (() {}),
        child: Text("Change Color"),
      ),
.
.
.
 MaterialStateProperty<Color> getColor(Color color, Color colorPressed) {
    final getColor = (Set<MaterialState> states) {
      if (states.contains(MaterialState.pressed)) {
        return colorPressed;
      } else {
        return color;
      }
    };
    return MaterialStateProperty.resolveWith(getColor);
  }
```

![image](https://user-images.githubusercontent.com/110470373/186069306-4bb87e7e-768f-4318-8bb2-61792741a3e8.png)


- Change the "foregroundColor" and "backgroundColor" property to make the button change.

```dart
foregroundColor: getColor(Colors.red, Colors.white),// 1- normal color, 2- pressed color
backgroundColor: getColor(Colors.white, Colors.red),// 1- normal color, 2- pressed color
```

![image](https://user-images.githubusercontent.com/110470373/186069708-c2af627a-42d5-4080-a49a-f28ff7813497.png)


## All Buttons

1. ElevatedButton

```dart
ElevatedButton(
  style: ElevatedButton.styleFrom(
    minimumSize: Size(150, 50),
    textStyle: TextStyle(fontSize: 18),
    primary: Colors.orange, //background
    onPrimary: Colors.black, // foreground
  ),
  onPressed: () => Fluttertoast.showToast(msg: 'Button is pressed', fontSize: 18),
  child: Text("Elevated Button"),
),
```

2. OutlinedButton

```dart
OutlinedButton(
  style: OutlinedButton.styleFrom(
      //minimumSize: Size.fromHeight(80),
      minimumSize: Size(150, 50),
      textStyle: TextStyle(fontSize: 18),
      primary: Colors.blue, // foreground
      side: BorderSide(color: Colors.blue, width: 2)),
  onPressed: () => Fluttertoast.showToast(msg: 'Button is pressed', fontSize: 18),
  child: Text("Outlined Button"),
),
```

3. TextButton

```dart
TextButton(
  style: TextButton.styleFrom(
    //minimumSize: Size.fromHeight(80),
    minimumSize: Size(150, 50),
    textStyle: TextStyle(fontSize: 18),
    primary: Colors.green, // foreground
  ),
  onPressed: () => Fluttertoast.showToast(msg: 'Button is pressed', fontSize: 18),
  child: Text("Outlined Button"),
),
```

4. IconsButton

```dart
IconButton(
  icon: Icon(
    Icons.settings,
    color: Colors.blueAccent,
  ),
  onPressed: () => Fluttertoast.showToast(msg: 'Button is pressed', fontSize: 18),
),
```

5. Icon in "ElevatedButton", "OutlinedButton" and "TextButton"

```dart
ElevatedButton.icon( // same property is applicable for all the 3 buttons
  onPressed: () => Fluttertoast.showToast(msg: 'Button is pressed', fontSize: 18),
  icon: Icon(
    Icons.settings,
    color: Colors.white,
  ),
  label: Text("Icon in Elevated Button"),
),
```


## Button Submit with Animation

```dart
enum ButtonState { init, loading, done }

class JButtonWithLoading extends StatefulWidget {
  const JButtonWithLoading({Key? key}) : super(key: key);

  @override
  State<JButtonWithLoading> createState() => _JButtonWithLoadingState();
}

class _JButtonWithLoadingState extends State<JButtonWithLoading> {
  ButtonState state = ButtonState.init;
  bool isAnimating = true;
  var isStretched = false;
  bool isLoading = false;
  var isDone = false;
  @override
  Widget build(BuildContext context) {
    double width = MediaQuery.of(context).size.width * 0.75;
    isStretched = isAnimating || state == ButtonState.init;
    isDone = state == ButtonState.done;
    return Column(
      children: [
        
        Container(
          child: AnimatedContainer(
              height: 50,
              duration: Duration(milliseconds: 300),
              curve: Curves.easeIn,
              width: state == ButtonState.init ? width : 70,
              onEnd: () => setState(() => isAnimating = !isAnimating),
              child: isStretched ? buildLoadingAnimator() : buildSmallLoading(isDone)),
        ),
      ],
    );
  }

  Widget buildLoadingAnimator() {
    return OutlinedButton(
      style: OutlinedButton.styleFrom(
        minimumSize: Size.fromHeight(50),
        textStyle: TextStyle(fontSize: 15),
        side: BorderSide(color: Colors.indigo, width: 2),
        shape: StadiumBorder(),
      ),
      onPressed: () async {
        setState(() {
          state = ButtonState.loading;
        });
        await Future.delayed(const Duration(seconds: 3));
        setState(() {
          state = ButtonState.done;
        });
        await Future.delayed(const Duration(seconds: 3));
        setState(() {
          state = ButtonState.init;
        });
        await Future.delayed(const Duration(seconds: 3));
      },
      child: FittedBox(
        child: Text(
          "Submit",
          style: TextStyle(fontSize: 24, color: Colors.indigo, fontWeight: FontWeight.w500),
        ),
      ),
    );
  }

  Widget buildSmallLoading(bool isDone) {
    Color color = isDone ? Colors.green : Colors.indigo;
    return Container(
      decoration: BoxDecoration(shape: BoxShape.circle, color: color),
      child: Center(
        child: isDone
            ? Icon(
                Icons.done,
                color: Colors.white,
              )
            : SizedBox(width: 30, height: 30, child: CircularProgressIndicator(color: Colors.white)),
      ),
    );
  }
}

```

## Material Banner

- A banner displays an important, succinct message, and provides actions for users to address (or dismiss the banner). A user action is required for it to be dismissed.

```dart
onPressed: () => ScaffoldMessenger.of(context).showMaterialBanner(
const MaterialBanner(
  padding: EdgeInsets.all(20),
  content: Text('Hello, I am a Material Banner'),
  leading: Icon(Icons.agriculture_outlined),
  backgroundColor: Colors.green,
  actions: <Widget>[
    TextButton(
      onPressed: (){
        ScaffoldMessenger.of(context).hideCurrentMaterialBanner();
      },
      child: Text('DISMISS'),
    ),
  ],
),

```

![image](https://user-images.githubusercontent.com/110470373/186084602-3fc51917-0db2-4038-b651-f6030067ad89.png)


## AppBar - methods

1. To give appbar a background image.

![image](https://user-images.githubusercontent.com/110470373/186326502-1e1921d0-2127-48af-9666-cf79be13cc33.png)

![image](https://user-images.githubusercontent.com/110470373/186326528-bf2b3d58-277c-45b8-b752-bc8978642016.png)

2. To set linear gradient colors.

![image](https://user-images.githubusercontent.com/110470373/186326588-b1d9927f-62f3-464c-9f84-fe4d34d8cd42.png)

![image](https://user-images.githubusercontent.com/110470373/186326620-54fc15c8-6c40-44cd-96a9-499bddba4661.png)


3. To customize tap bar in appBar

- Here you can customize the active tab, indicator.
- If you have more tabs, you can set "isScrollable" to true. So it will be scrollable

![image](https://user-images.githubusercontent.com/110470373/186326741-df687639-c8ad-4098-a803-4ddf37224db4.png)

![image](https://user-images.githubusercontent.com/110470373/186326797-e532c708-ae3e-445f-84c0-bb4f8adee20c.png)



## LayOut in Flutter

1. Stack

![image](https://user-images.githubusercontent.com/110470373/186398025-05a8440a-c2b9-4ae0-b3e5-9d2bb420df53.png)


2. Position

![image](https://user-images.githubusercontent.com/110470373/186399075-aacbb128-ebc7-4599-bdfb-c18c48ba9cd4.png)


## Chips

- Chips allow users to enter information, make selections, filter content, or trigger actions. 

- There are four different types of chips that are subclasses of Chip, including 
1.input, 
2.choice, 
3.filter,
4.action.

![image](https://user-images.githubusercontent.com/110470373/186402380-b862b961-8a98-4798-8ce9-b8e3708c4030.png)

```dart

InputChip(
  avatar: Icon(Icons.remove),
  label: Text('Input 1'),
  onSelected: (bool value) {},
),

ChoiceChip(
   label: Text('Choice 1'),
   selected: true,
 ),

FilterChip(
  label: Text('Filter 1'),
  selected: true,
  onSelected: (bool value) {},
),

ActionChip(
  avatar: Icon(Icons.favorite),
  label: Text('Action 1'),
  onPressed: () {},
),
```

## Dio 
- Dio package comes handy as it provides a powerful HTTP client for Dart and Flutter and it supports Interceptors, Global configuration, FormData, Request Cancellation, File Downloading, Timeout etc.



```dart
import 'package:dio/dio.dart';

class DioClient {
  final Dio _dio = Dio();

  final _baseUrl = 'https://reqres.in/api';

}

```

1. **Defining the GET request**

```dart

  Future<User?> getUser({required String id}) async {
  User? user;
  try {
    Response userData = await _dio.get(_baseUrl + '/users/$id');
    print('User Info: ${userData.data}');
    user = User.fromJson(userData.data);
  } on DioError catch (e) {
    // The request was made and the server responded with a status code
    // that falls out of the range of 2xx and is also not 304.
    if (e.response != null) {
      print('Dio error!');
      print('STATUS: ${e.response?.statusCode}');
      print('DATA: ${e.response?.data}');
      print('HEADERS: ${e.response?.headers}');
    } else {
      // Error due to setting up or sending the request
      print('Error sending request!');
      print(e.message);
    }
  }
  return user;
}
```

2. **Defining the POST request**

```dart
Future<UserInfo?> createUser({required UserInfo userInfo}) async {
  UserInfo? retrievedUser;

  try {
    Response response = await _dio.post(
      _baseUrl + '/users',
      data: userInfo.toJson(),
    );

    print('User created: ${response.data}');

    retrievedUser = UserInfo.fromJson(response.data);
  } catch (e) {
    print('Error creating user: $e');
  }

  return retrievedUser;
}
```

3. **Defining the PUT request**

```dart

Future<UserInfo?> updateUser({
  required UserInfo userInfo,
  required String id,
}) async {
  UserInfo? updatedUser;

  try {
    Response response = await _dio.put(
      _baseUrl + '/users/$id',
      data: userInfo.toJson(),
    );

    print('User updated: ${response.data}');

    updatedUser = UserInfo.fromJson(response.data);
  } catch (e) {
    print('Error updating user: $e');
  }

  return updatedUser;
}
```

4. **Defining the DELETE request**

```dart
Future<void> deleteUser({required String id}) async {
  try {
    await _dio.delete(_baseUrl + '/users/$id');
    print('User deleted!');
  } catch (e) {
    print('Error deleting user: $e');
  }
}
```

### Defining

- Instead of passing the endpoint with baseUrl every time, you can just define it inside BaseOptions and pass it once while instantiating Dio.

```dart
final Dio _dio = Dio(
  BaseOptions(
    baseUrl: 'https://reqres.in/api',
    connectTimeout: 5000,
    receiveTimeout: 3000,
  ),
);
```

### Uploading files

- You can easily upload files to a server using FormData and Dio

```dart
String imagePath;

FormData formData = FormData.fromMap({
  "image": await MultipartFile.fromFile(
    imagePath,
    filename: "upload.jpeg",
  ),
});

Response response = await _dio.post(
  '/search',
  data: formData,
  onSendProgress: (int sent, int total) {
    print('$sent $total');
  },
);
```

### Interceptors

- You can intercept Dio requests, responses, and errors before they are handled by using then or catchError
- You can run the interceptor by overriding the callbacks at three places: onRequest, onResponse, and onError.

```dart
class AppInterceptors extends Interceptor {
  @override
  FutureOr<dynamic> onRequest(RequestOptions options) async {
    if (options.headers.containsKey("requiresToken")) {
      //remove the auxiliary header
      options.headers.remove("requiresToken");

      SharedPreferences prefs = await SharedPreferences.getInstance();
      var header = prefs.get("Header");

      options.headers.addAll({"Header": "$header${DateTime.now()}"});

      return options;
    }
  }

  @override
  FutureOr<dynamic> onError(DioError dioError) {
    if (dioError.message.contains("ERROR_001")) {
      // this will push a new route and remove all the routes that were present
      navigatorKey.currentState.pushNamedAndRemoveUntil(
          "/login", (Route<dynamic> route) => false);
    }

    return dioError;
  }

  @override
  FutureOr<dynamic> onResponse(Response options) async {
    if (options.headers.value("verifyToken") != null) {
      //if the header is present, then compare it with the Shared Prefs key
      SharedPreferences prefs = await SharedPreferences.getInstance();
      var verifyToken = prefs.get("VerifyToken");

      // if the value is the same as the header, continue with the request
      if (options.headers.value("verifyToken") == verifyToken) {
        return options;
      }
    }

    return DioError(request: options.request, message: "User is no longer active");
  }
}
```

```dart
class Logging extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    print('REQUEST[${options.method}] => PATH: ${options.path}');
    return super.onRequest(options, handler);
  }

  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    print(
      'RESPONSE[${response.statusCode}] => PATH: ${response.requestOptions.path}',
    );
    return super.onResponse(response, handler);
  }

  @override
  void onError(DioError err, ErrorInterceptorHandler handler) {
    print(
      'ERROR[${err.response?.statusCode}] => PATH: ${err.requestOptions.path}',
    );
    return super.onError(err, handler);
  }
}
```

```dart
final Dio _dio = Dio(
    BaseOptions(
      baseUrl: 'https://reqres.in/api',
      connectTimeout: 5000,
      receiveTimeout: 3000,
    ),
  )..interceptors.add(Logging());
```



## Syncfusion - Datagrid

- Convert Json to DataSource and to SfDataGrid.

![image](https://user-images.githubusercontent.com/110470373/187169020-d1655803-912e-4afb-b0a0-c5331a5cea48.png)

![image](https://user-images.githubusercontent.com/110470373/187169508-e4ae615b-41d0-4313-bb03-d3a67391e289.png)

```dart
Future<List<Product>> generateProductList() async {
    var response = await http.get(Uri.parse('https://ej2services.syncfusion.com/production/web-services/api/Orders'));
    var decodedProducts = json.decode(response.body).cast<Map<String, dynamic>>();
    List<Product> productList = await decodedProducts.map<Product>((json) => Product.fromJson(json)).toList();
    return productList;
  }
```

- Need to convert the list to DataGridSouce.

```dart
class EmployeeDataSource extends DataGridSource {
  EmployeeDataSource({List<Employee> employees}) {
     _employees = employees
        .map<DataGridRow>((e) => DataGridRow(cells: [
              DataGridCell<int>(columnName: 'id', value: e.id),
              DataGridCell<String>(columnName: 'name', value: e.name),
              DataGridCell<String>(
                  columnName: 'designation', value: e.designation),
              DataGridCell<int>(columnName: 'salary', value: e.salary),
            ]))
        .toList();
  }

  List<DataGridRow>  _employees = [];

  @override
  List<DataGridRow> get rows =>  _employees;

  @override
  DataGridRowAdapter? buildRow(DataGridRow row) {
    return DataGridRowAdapter(
        cells: row.getCells().map<Widget>((dataGridCell) {
      return Container(
        alignment: (dataGridCell.columnName == 'id' || dataGridCell.columnName == 'salary')
            ? Alignment.centerRight
            : Alignment.centerLeft,
        padding: EdgeInsets.all(16.0),
        child: Text(dataGridCell.value.toString()),
      );
    }).toList());
  }
}

```


```dart
Scaffold(
    appBar: AppBar(
      title: const Text('Syncfusion Flutter DataGrid'),
    ),
    body: SfDataGrid(
      source: employeeDataSource,
      columns: <GridColumn>[
        GridColumn(
            columnName: 'id',
            label: Container(
                padding: EdgeInsets.all(16.0),
                alignment: Alignment.centerRight,
                child: Text(
                  'ID',
                ))),
        GridColumn(
            columnName: 'name',
            label: Container(
                padding: EdgeInsets.all(16.0),
                alignment: Alignment.centerLeft,
                child: Text('Name'))),
        GridColumn(
            columnName: 'designation',
            width: 120,
            label: Container(
                padding: EdgeInsets.all(16.0),
                alignment: Alignment.centerLeft,
                child: Text('Designation'))),
        GridColumn(
            columnName: 'salary',
            label: Container(
                padding: EdgeInsets.all(16.0),
                alignment: Alignment.centerRight,
                child: Text('Salary'))),
      ],
    ),
  );
```


#### Sorting

```dart
SortingDataSource sortingDataGridSource=snapshot.data;
SfDataGrid(
  source:sortingDataGridSource ,
  columns: getColumns(),
  allowSorting: true, // allow to do sorting
  allowTriStateSorting: true, // State of sorting will be three
  allowMultiColumnSorting: true, // can to multiple sorting
  sortingGestureType: SortingGestureType.doubleTap, // make sort when you do double tap
)
```

- To disable sorting to particular column

```dart
 GridColumn(
 columnName: 'orderID',
 allowSorting:false,
 width: 70,
 label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerLeft, child: Text('OrderID', overflow: TextOverflow.clip, softWrap: true))
 ),
      
```


```dart
ElevatedButton(
onPressed: () {
  sortingDataGridSource.sortedColumns.add(SortColumnDetails(
      name: 'name',
      sortDirection: DataGridSortDirection.ascending));
  sortingDataGridSource.sort();
},
child: Text('Apply sort')))
```


- Create custom sorting - case sensitive

![image](https://user-images.githubusercontent.com/110470373/187175759-64cd59a1-c2d0-40f3-a560-cbbe953029bb.png)


```dart

  @override
  int compare(DataGridRow? a, DataGridRow? b, SortColumnDetails sortColumn) {
    if (sortColumn.name == 'name') {
      final String? value1 = a
          ?.getCells()
          .firstWhereOrNull((element) => element.columnName == sortColumn.name)
          ?.value
          .toString();
      final String? value2 = b
          ?.getCells()
          .firstWhereOrNull((element) => element.columnName == sortColumn.name)
          ?.value
          .toString();

      if (value1 == null || value2 == null) {
        return 0;
      }

      if (sortColumn.sortDirection == DataGridSortDirection.ascending) {
        return value1.toLowerCase().compareTo(value2.toLowerCase());
      } else {
        return value2.toLowerCase().compareTo(value1.toLowerCase());
      }
    }
    return super.compare(a, b, sortColumn);
  }

```

#### Width Resizing

```dart
  late Map<String, double> columnWidths = {
    'orderID': double.nan, 
    'customerID': double.nan, 
    'employeeID': double.nan,
     'orderDate': double.nan, 
     'freight': double.nan};

```

```dart
SfDataGrid(
  source: snapshot.data,
  columns: getColumns(),
  allowColumnsResizing: true,
  columnResizeMode: ColumnResizeMode.onResizeEnd,
  onColumnResizeUpdate: (ColumnResizeUpdateDetails details) {
    setState(() {
      columnWidths[details.column.columnName] = details.width;
    });
    return true;
  },
)
```

```dart
List<GridColumn> getColumns() {
    return <GridColumn>[
      GridColumn(
          columnName: 'orderID',
          width: columnWidths['orderID']!,
          label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerLeft, child: Text('Order ID', overflow: TextOverflow.clip, softWrap: true))),
      GridColumn(
          columnName: 'customerID',
          width: columnWidths['customerID']!,
          label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerRight, child: Text('Customer ID', overflow: TextOverflow.clip, softWrap: true))),
      GridColumn(
          columnName: 'employeeID',
          width: columnWidths['employeeID']!,
          label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerLeft, child: Text('Employee ID', overflow: TextOverflow.clip, softWrap: true))),
      GridColumn(
          columnName: 'orderDate',
          width: columnWidths['orderDate']!,
          label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerRight, child: Text('Order Date', overflow: TextOverflow.clip, softWrap: true))),
      GridColumn(
        columnName: 'freight',
        width: columnWidths['freight']!,
        label: Container(padding: EdgeInsets.all(8), alignment: Alignment.centerLeft, child: Text('Freight')),
      )
    ];
```


## flutter_offline 2.1.0

- A package to check online and offline of the application.
- To know more [click here](https://pub.dev/packages/flutter_offline)

```dart
OfflineBuilder(
  connectivityBuilder: (
    BuildContext context,
    ConnectivityResult connectivity,
    Widget child,
  ) {
    final bool connected = connectivity != ConnectivityResult.none;
    print("Reran");
    return new Stack(
      fit: StackFit.expand,
      children: [
        Positioned(
          height: 24.0,
          left: 0.0,
          right: 0.0,
          child: Container(
            color: connected ? Color(0xFF00EE44) : Color(0xFFEE4400),
            child: Center(
              child: Text("${connected ? 'ONLINE' : 'OFFLINE'}"),
            ),
          ),
        ),
        Center(
          child: new Text(
            'Yay!',
          ),
        ),
      ],
    );
  },
),
```

## flutter_js

- A package used to compile js code.
- To know more [Click Here](https://pub.dev/packages/flutter_js)

```dart
dependencies:
  flutter_js: 0.1.0+0
```

```dart
JavascriptRuntime flutterJs=flutterJs = getJavascriptRuntime();
.
.
.
JsEvalResult jsResult = flutterJs.evaluate(
    "Math.trunc(Math.random() * 100).toString();");
setState(() {
  _jsResult = jsResult.stringResult;
});

```

![image](https://user-images.githubusercontent.com/110470373/187417935-5f860746-134d-46d5-8435-a3ef0917c686.png)


## image_downloader

- A package to download images from online and save to Photo library.
- To know more [Click here](https://pub.dev/packages/image_downloader)

1. Add this permission in AndroidManifest.xml.

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

```dart
try {
  // Saved with this method.
  var imageId = await ImageDownloader.downloadImage("https://raw.githubusercontent.com/wiki/ko2ic/image_downloader/images/flutter.png",destination:AndroidDestinationType.directoryDownloads..subDirectory("flutter_image.png"));
  if (imageId == null) {
    return;
  }

  // Below is a method of obtaining saved image information.
  var fileName = await ImageDownloader.findName(imageId);
  var path = await ImageDownloader.findPath(imageId);
  var size = await ImageDownloader.findByteSize(imageId);
  var mimeType = await ImageDownloader.findMimeType(imageId);
} on PlatformException catch (error) {
  print(error);
}
```

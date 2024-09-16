import 'package:auto_route/auto_route.dart';  
import 'package:flutter/material.dart';  
import 'nested_navigation.router.gr.dart';  
  
void main() {  
  
  runApp(NestedNavigationApp());  
}  
  
class NestedNavigationApp extends StatelessWidget {  
  NestedNavigationApp({super.key});  
  
  final nestedRouter = NestedRouter();  
  
  @override  
  Widget build(BuildContext context) {  
    return MaterialApp.router(  
      routerConfig: nestedRouter.config(),  
    );  
  }  
}  
  
@AutoRouterConfig()  
class NestedRouter extends RootStackRouter {  
  @override  
  List<AutoRoute> get routes => [  
    AutoRoute(  
      initial: true,  
      page: HostRoute.page,  
      children: [  
        AutoRoute(page: FirstRoute.page, initial: true),  
        AutoRoute(page: SecondRoute.page),  
        AutoRoute(page: ThirdRoute.page),  
      ],  
    ),  
  ];  
}  
  
@RoutePage()  
class HostScreen extends StatefulWidget {  
  
  const HostScreen({super.key});  
  
  @override  
  State<HostScreen> createState() => _HostScreenState();  
}  
  
class _HostScreenState extends State<HostScreen> {  
  
  @override  
  void initState() {  
    super.initState();  
  }  
  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      appBar: AppBar(  
        title: Text('Host Screen'),  
  
        /// This will automatically display a back button if the nested router can pop  
        leading: AutoLeadingButton(),  
      ),  
      body: AutoRouter(),  
    );  
  }  
}  
  
@RoutePage()  
class FirstScreen extends StatelessWidget {  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Center(  
        child: Column(  
          mainAxisSize: MainAxisSize.min,  
          children: [  
            Text('First screen'),  
            ElevatedButton(  
              onPressed: () => context.router.push(SecondRoute()),//context.pushRoute(SecondRoute())  
              child: Text('Go to second screen'),  
            ),  
          ],  
        ),  
      ),  
    );  
  }  
}  
  
@RoutePage()  
class SecondScreen extends StatelessWidget {  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Center(  
        child: Column(  
          mainAxisSize: MainAxisSize.min,  
          children: [  
            Text('Second screen'),  
            SizedBox(height: 16,),  
            ElevatedButton(  
              onPressed: () => context.pushRoute(ThirdRoute()),  
              child: Text('Go third route'),  
            ),  
            SizedBox(height: 16,),  
            ElevatedButton(  
              onPressed: () => context.maybePop(),  
              child: Text('Go Back'),  
            ),  
          ],  
        ),  
      ),  
    );  
  }  
}  
  
@RoutePage()  
class ThirdScreen extends StatelessWidget {  
  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Center(  
        child: Column(  
          mainAxisSize: MainAxisSize.min,  
          children: [  
            Text('Third screen'),  
            SizedBox(height: 16,),  
            ElevatedButton(  
              onPressed: () => context.maybePop(),  
              child: Text('Go Back'),  
            ),  
          ],  
        ),  
      ),  
    );  
  }  
}
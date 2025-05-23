# Astra

# Create new Flutter project
flutter create astra
cd astra

# Add required dependencies
flutter pub add firebase_core
flutter pub add firebase_auth
flutter pub add cloud_firestore
flutter pub add google_maps_flutter
flutter pub add location
flutter pub add permission_handler
flutter pub add socket_io_client
flutter pub add provider
flutter pub add shared_preferences
flutter pub add uuid

# Development dependencies
flutter pub add --dev flutter_launcher_icons
flutter pub add --dev flutter_native_splash

# Verify dependencies
flutter pub get

# Test the setup
flutter run



lib/
├── main.dart                 # App entry point
├── models/                   # Data models
│   ├── user.dart
│   ├── group.dart
│   ├── ride.dart
│   └── location.dart
├── screens/                  # UI screens
│   ├── auth/
│   │   ├── login_screen.dart
│   │   └── register_screen.dart
│   ├── home/
│   │   └── home_screen.dart
│   ├── group/
│   │   ├── create_group_screen.dart
│   │   ├── group_details_screen.dart
│   │   └── join_group_screen.dart
│   ├── map/
│   │   └── map_screen.dart
│   └── profile/
│       └── profile_screen.dart
├── services/                 # Business logic
│   ├── auth_service.dart
│   ├── firebase_service.dart
│   ├── location_service.dart
│   ├── map_service.dart
│   └── group_service.dart
├── providers/                # State management
│   ├── auth_provider.dart
│   ├── group_provider.dart
│   └── location_provider.dart
├── widgets/                  # Reusable UI components
│   ├── common/
│   │   ├── custom_button.dart
│   │   ├── custom_text_field.dart
│   │   └── loading_widget.dart
│   └── map/
│       ├── rider_marker.dart
│       └── route_overlay.dart
├── utils/                    # Utilities
│   ├── constants.dart
│   ├── helpers.dart
│   └── validators.dart
└── config/                   # Configuration
    ├── app_config.dart
    └── theme.dart


main.dart:
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:provider/provider.dart';
import 'providers/auth_provider.dart';
import 'providers/group_provider.dart';
import 'providers/location_provider.dart';
import 'screens/auth/login_screen.dart';
import 'screens/home/home_screen.dart';
import 'config/theme.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Initialize Firebase
  await Firebase.initializeApp();
  
  runApp(const AstraApp());
}

class AstraApp extends StatelessWidget {
  const AstraApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => AuthProvider()),
        ChangeNotifierProvider(create: (_) => GroupProvider()),
        ChangeNotifierProvider(create: (_) => LocationProvider()),
      ],
      child: MaterialApp(
        title: 'Astra - Motorcycle Group Navigation',
        theme: AppTheme.lightTheme,
        darkTheme: AppTheme.darkTheme,
        themeMode: ThemeMode.system,
        home: const AuthWrapper(),
        debugShowCheckedModeBanner: false,
      ),
    );
  }
}

class AuthWrapper extends StatelessWidget {
  const AuthWrapper({super.key});

  @override
  Widget build(BuildContext context) {
    return Consumer<AuthProvider>(
      builder: (context, authProvider, _) {
        if (authProvider.isLoading) {
          return const Scaffold(
            body: Center(
              child: CircularProgressIndicator(),
            ),
          );
        }
        
        return authProvider.isAuthenticated 
            ? const HomeScreen() 
            : const LoginScreen();
      },
    );
  }
}



theme.dart
import 'package:flutter/material.dart';

class AppTheme {
  // Colors
  static const Color primaryColor = Color(0xFF2196F3); // Blue
  static const Color accentColor = Color(0xFFFF5722);  // Orange
  static const Color backgroundColor = Color(0xFFF5F5F5);
  static const Color cardColor = Color(0xFFFFFFFF);
  static const Color textPrimary = Color(0xFF212121);
  static const Color textSecondary = Color(0xFF757575);
  static const Color errorColor = Color(0xFFD32F2F);
  static const Color successColor = Color(0xFF388E3C);
  
  // Motorcycle-friendly colors for map
  static const Color riderColor = Color(0xFF4CAF50);    // Green for riders
  static const Color leaderColor = Color(0xFFFF9800);   // Orange for leader
  static const Color routeColor = Color(0xFF2196F3);    // Blue for route
  static const Color stopColor = Color(0xFFF44336);     // Red for stops
  static const Color emergencyColor = Color(0xFFE91E63); // Pink for emergency

  static ThemeData get lightTheme {
    return ThemeData(
      useMaterial3: true,
      primarySwatch: Colors.blue,
      primaryColor: primaryColor,
      scaffoldBackgroundColor: backgroundColor,
      cardColor: cardColor,
      
      // App Bar Theme
      appBarTheme: const AppBarTheme(
        backgroundColor: primaryColor,
        foregroundColor: Colors.white,
        elevation: 2,
        centerTitle: true,
        titleTextStyle: TextStyle(
          fontSize: 20,
          fontWeight: FontWeight.bold,
          color: Colors.white,
        ),
      ),
      
      // Button Themes
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          backgroundColor: primaryColor,
          foregroundColor: Colors.white,
          padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 12),
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(8),
          ),
          textStyle: const TextStyle(
            fontSize: 16,
            fontWeight: FontWeight.w600,
          ),
        ),
      ),
      
      // Input Field Theme
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Colors.white,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: Colors.grey),
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: Colors.grey),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: primaryColor, width: 2),
        ),
        contentPadding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
      ),
      
      // Card Theme
      cardTheme: CardTheme(
        color: cardColor,
        elevation: 2,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
        ),
      ),
      
      // Text Themes
      textTheme: const TextTheme(
        headlineLarge: TextStyle(
          fontSize: 28,
          fontWeight: FontWeight.bold,
          color: textPrimary,
        ),
        headlineMedium: TextStyle(
          fontSize: 24,
          fontWeight: FontWeight.w600,
          color: textPrimary,
        ),
        bodyLarge: TextStyle(
          fontSize: 16,
          color: textPrimary,
        ),
        bodyMedium: TextStyle(
          fontSize: 14,
          color: textSecondary,
        ),
      ),
      
      // Bottom Navigation Theme
      bottomNavigationBarTheme: const BottomNavigationBarThemeData(
        backgroundColor: Colors.white,
        selectedItemColor: primaryColor,
        unselectedItemColor: textSecondary,
        type: BottomNavigationBarType.fixed,
        elevation: 8,
      ),
    );
  }

  static ThemeData get darkTheme {
    return lightTheme.copyWith(
      brightness: Brightness.dark,
      scaffoldBackgroundColor: const Color(0xFF121212),
      cardColor: const Color(0xFF1E1E1E),
      
      appBarTheme: lightTheme.appBarTheme.copyWith(
        backgroundColor: const Color(0xFF1E1E1E),
      ),
      
      bottomNavigationBarTheme: lightTheme.bottomNavigationBarTheme.copyWith(
        backgroundColor: const Color(0xFF1E1E1E),
      ),
      
      textTheme: lightTheme.textTheme.copyWith(
        headlineLarge: lightTheme.textTheme.headlineLarge?.copyWith(
          color: Colors.white,
        ),
        headlineMedium: lightTheme.textTheme.headlineMedium?.copyWith(
          color: Colors.white,
        ),
        bodyLarge: lightTheme.textTheme.bodyLarge?.copyWith(
          color: Colors.white,
        ),
      ),
    );
  }
}


user.dart
class User {
  final String id;
  final String name;
  final String email;
  final String? phoneNumber;
  final String? profileImageUrl;
  final DateTime createdAt;
  final DateTime lastSeen;

  User({
    required this.id,
    required this.name,
    required this.email,
    this.phoneNumber,
    this.profileImageUrl,
    required this.createdAt,
    required this.lastSeen,
  });

  // Convert from Firebase document
  factory User.fromMap(Map<String, dynamic> map, String id) {
    return User(
      id: id,
      name: map['name'] ?? '',
      email: map['email'] ?? '',
      phoneNumber: map['phoneNumber'],
      profileImageUrl: map['profileImageUrl'],
      createdAt: DateTime.parse(map['createdAt']),
      lastSeen: DateTime.parse(map['lastSeen']),
    );
  }

  // Convert to Firebase document
  Map<String, dynamic> toMap() {
    return {
      'name': name,
      'email': email,
      'phoneNumber': phoneNumber,
      'profileImageUrl': profileImageUrl,
      'createdAt': createdAt.toIso8601String(),
      'lastSeen': lastSeen.toIso8601String(),
    };
  }

  // Create copy with updated fields
  User copyWith({
    String? name,
    String? email,
    String? phoneNumber,
    String? profileImageUrl,
    DateTime? lastSeen,
  }) {
    return User(
      id: id,
      name: name ?? this.name,
      email: email ?? this.email,
      phoneNumber: phoneNumber ?? this.phoneNumber,
      profileImageUrl: profileImageUrl ?? this.profileImageUrl,
      createdAt: createdAt,
      lastSeen: lastSeen ?? this.lastSeen,
    );
  }

  @override
  String toString() {
    return 'User(id: $id, name: $name, email: $email)';
  }
}


Group.dart
import 'location.dart';

enum GroupStatus {
  planning,    // Group created, planning route
  active,      // Currently riding
  paused,      // Stopped/break
  completed,   // Ride finished
}

enum MemberRole {
  leader,      // Can set destinations, approve stops
  member,      // Can suggest stops, send alerts
}

class GroupMember {
  final String userId;
  final String name;
  final MemberRole role;
  final DateTime joinedAt;
  final bool isOnline;
  final RiderLocation? currentLocation;

  GroupMember({
    required this.userId,
    required this.name,
    required this.role,
    required this.joinedAt,
    this.isOnline = false,
    this.currentLocation,
  });

  factory GroupMember.fromMap(Map<String, dynamic> map) {
    return GroupMember(
      userId: map['userId'],
      name: map['name'],
      role: MemberRole.values.firstWhere(
        (role) => role.name == map['role'],
        orElse: () => MemberRole.member,
      ),
      joinedAt: DateTime.parse(map['joinedAt']),
      isOnline: map['isOnline'] ?? false,
      currentLocation: map['currentLocation'] != null
          ? RiderLocation.fromMap(map['currentLocation'])
          : null,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'userId': userId,
      'name': name,
      'role': role.name,
      'joinedAt': joinedAt.toIso8601String(),
      'isOnline': isOnline,
      'currentLocation': currentLocation?.toMap(),
    };
  }

  GroupMember copyWith({
    String? name,
    MemberRole? role,
    bool? isOnline,
    RiderLocation? currentLocation,
  }) {
    return GroupMember(
      userId: userId,
      name: name ?? this.name,
      role: role ?? this.role,
      joinedAt: joinedAt,
      isOnline: isOnline ?? this.isOnline,
      currentLocation: currentLocation ?? this.currentLocation,
    );
  }
}

class Group {
  final String id;
  final String name;
  final String description;
  final String leaderId;
  final List<GroupMember> members;
  final GroupStatus status;
  final RiderLocation? destination;
  final List<RiderLocation> suggestedStops;
  final List<RiderLocation> approvedStops;
  final DateTime createdAt;
  final DateTime? startedAt;
  final DateTime? completedAt;
  final String joinCode;

  Group({
    required this.id,
    required this.name,
    required this.description,
    required this.leaderId,
    required this.members,
    required this.status,
    this.destination,
    this.suggestedStops = const [],
    this.approvedStops = const [],
    required this.createdAt,
    this.startedAt,
    this.completedAt,
    required this.joinCode,
  });

  factory Group.fromMap(Map<String, dynamic> map, String id) {
    return Group(
      id: id,
      name: map['name'],
      description: map['description'] ?? '',
      leaderId: map['leaderId'],
      members: (map['members'] as List?)
          ?.map((member) => GroupMember.fromMap(member))
          .toList() ?? [],
      status: GroupStatus.values.firstWhere(
        (status) => status.name == map['status'],
        orElse: () => GroupStatus.planning,
      ),
      destination: map['destination'] != null
          ? RiderLocation.fromMap(map['destination'])
          : null,
      suggestedStops: (map['suggestedStops'] as List?)
          ?.map((stop) => RiderLocation.fromMap(stop))
          .toList() ?? [],
      approvedStops: (map['approvedStops'] as List?)
          ?.map((stop) => RiderLocation.fromMap(stop))
          .toList() ?? [],
      createdAt: DateTime.parse(map['createdAt']),
      startedAt: map['startedAt'] != null 
          ? DateTime.parse(map['startedAt']) 
          : null,
      completedAt: map['completedAt'] != null 
          ? DateTime.parse(map['completedAt']) 
          : null,
      joinCode: map['joinCode'],
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'name': name,
      'description': description,
      'leaderId': leaderId,
      'members': members.map((member) => member.toMap()).toList(),
      'status': status.name,
      'destination': destination?.toMap(),
      'suggestedStops': suggestedStops.map((stop) => stop.toMap()).toList(),
      'approvedStops': approvedStops.map((stop) => stop.toMap()).toList(),
      'createdAt': createdAt.toIso8601String(),
      'startedAt': startedAt?.toIso8601String(),
      'completedAt': completedAt?.toIso8601String(),
      'joinCode': joinCode,
    };
  }

  // Helper methods
  bool get isActive => status == GroupStatus.active;
  bool get isLeader => members.any((m) => m.role == MemberRole.leader);
  int get memberCount => members.length;
  List<GroupMember> get onlineMembers => members.where((m) => m.isOnline).toList();

  Group copyWith({
    String? name,
    String? description,
    List<GroupMember>? members,
    GroupStatus? status,
    RiderLocation? destination,
    List<RiderLocation>? suggestedStops,
    List<RiderLocation>? approvedStops,
    DateTime? startedAt,
    DateTime? completedAt,
  }) {
    return Group(
      id: id,
      name: name ?? this.name,
      description: description ?? this.description,
      leaderId: leaderId,
      members: members ?? this.members,
      status: status ?? this.status,
      destination: destination ?? this.destination,
      suggestedStops: suggestedStops ?? this.suggestedStops,
      approvedStops: approvedStops ?? this.approvedStops,
      createdAt: createdAt,
      startedAt: startedAt ?? this.startedAt,
      completedAt: completedAt ?? this.completedAt,
      joinCode: joinCode,
    );
  }
}


location.dart
enum LocationType {
  rider,        // Current rider position
  destination,  // Final destination
  stop,         // Approved stop point
  suggested,    // Suggested stop
  emergency,    // Emergency location
}

enum EmergencyType {
  breakdown,    // Mechanical issue
  accident,     // Accident occurred
  medical,      // Medical emergency
  fuel,         // Out of fuel
  weather,      // Weather-related stop
}

class RiderLocation {
  final double latitude;
  final double longitude;
  final double? altitude;
  final double? accuracy;
  final double? speed;
  final double? heading;
  final DateTime timestamp;
  final String? address;
  final LocationType type;
  final String? description;
  final String? userId;
  final EmergencyType? emergencyType;
  final bool isActive;

  RiderLocation({
    required this.latitude,
    required this.longitude,
    this.altitude,
    this.accuracy,
    this.speed,
    this.heading,
    required this.timestamp,
    this.address,
    required this.type,
    this.description,
    this.userId,
    this.emergencyType,
    this.isActive = true,
  });

  factory RiderLocation.fromMap(Map<String, dynamic> map) {
    return RiderLocation(
      latitude: map['latitude']?.toDouble() ?? 0.0,
      longitude: map['longitude']?.toDouble() ?? 0.0,
      altitude: map['altitude']?.toDouble(),
      accuracy: map['accuracy']?.toDouble(),
      speed: map['speed']?.toDouble(),
      heading: map['heading']?.toDouble(),
      timestamp: DateTime.parse(map['timestamp']),
      address: map['address'],
      type: LocationType.values.firstWhere(
        (type) => type.name == map['type'],
        orElse: () => LocationType.rider,
      ),
      description: map['description'],
      userId: map['userId'],
      emergencyType: map['emergencyType'] != null
          ? EmergencyType.values.firstWhere(
              (type) => type.name == map['emergencyType'],
            )
          : null,
      isActive: map['isActive'] ?? true,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'latitude': latitude,
      'longitude': longitude,
      'altitude': altitude,
      'accuracy': accuracy,
      'speed': speed,
      'heading': heading,
      'timestamp': timestamp.toIso8601String(),
      'address': address,
      'type': type.name,
      'description': description,
      'userId': userId,
      'emergencyType': emergencyType?.name,
      'isActive': isActive,
    };
  }

  // Calculate distance to another location in meters
  double distanceTo(RiderLocation other) {
    const double earthRadius = 6371000; // Earth's radius in meters
    
    double lat1Rad = latitude * (3.14159265359 / 180);
    double lat2Rad = other.latitude * (3.14159265359 / 180);
    double deltaLatRad = (other.latitude - latitude) * (3.14159265359 / 180);
    double deltaLngRad = (other.longitude - longitude) * (3.14159265359 / 180);

    double a = (deltaLatRad / 2).sin() * (deltaLatRad / 2).sin() +
        lat1Rad.cos() * lat2Rad.cos() *
        (deltaLngRad / 2).sin() * (deltaLngRad / 2).sin();
    
    double c = 2 * (a.sqrt()).atan2((1 - a).sqrt());
    
    return earthRadius * c;
  }

  // Check if location is recent (within last 30 seconds)
  bool get isRecent {
    return DateTime.now().difference(timestamp).inSeconds < 30;
  }

  // Check if this is an emergency location
  bool get isEmergency => type == LocationType.emergency;

  // Get formatted speed
  String get speedKmh {
    if (speed == null) return 'N/A';
    return '${(speed! * 3.6).toStringAsFixed(1)} km/h';
  }

  // Get formatted address or coordinates
  String get displayLocation {
    return address ?? '${latitude.toStringAsFixed(6)}, ${longitude.toStringAsFixed(6)}';
  }

  RiderLocation copyWith({
    double? latitude,
    double? longitude,
    double? altitude,
    double? accuracy,
    double? speed,
    double? heading,
    DateTime? timestamp,
    String? address,
    LocationType? type,
    String? description,
    String? userId,
    EmergencyType? emergencyType,
    bool? isActive,
  }) {
    return RiderLocation(
      latitude: latitude ?? this.latitude,
      longitude: longitude ?? this.longitude,
      altitude: altitude ?? this.altitude,
      accuracy: accuracy ?? this.accuracy,
      speed: speed ?? this.speed,
      heading: heading ?? this.heading,
      timestamp: timestamp ?? this.timestamp,
      address: address ?? this.address,
      type: type ?? this.type,
      description: description ?? this.description,
      userId: userId ?? this.userId,
      emergencyType: emergencyType ?? this.emergencyType,
      isActive: isActive ?? this.isActive,
    );
  }

  @override
  String toString() {
    return 'RiderLocation(lat: $latitude, lng: $longitude, type: ${type.name}, time: $timestamp)';
  }
}

constants.dart
class AppConstants {
  // App Info
  static const String appName = 'Astra';
  static const String appVersion = '1.0.0';
  
  // Firebase Collections
  static const String usersCollection = 'users';
  static const String groupsCollection = 'groups';
  static const String ridesCollection = 'rides';
  
  // Location Settings
  static const double locationUpdateInterval = 5.0; // seconds
  static const double minDistanceFilter = 10.0; // meters
  static const int locationTimeoutSeconds = 30;
  
  // Group Settings
  static const int maxGroupMembers = 10;
  static const int groupCodeLength = 6;
  
  // Map Settings
  static const double defaultZoom = 15.0;
  static const double riderMarkerSize = 40.0;
  static const double stopMarkerSize = 35.0;
  
  // Emergency Settings
  static const int emergencyTimeoutSeconds = 300; // 5 minutes
  
  // Socket Events
  static const String socketConnect = 'connect';
  static const String socketDisconnect = 'disconnect';
  static const String socketJoinGroup = 'join_group';
  static const String socketLeaveGroup = 'leave_group';
  static const String socketLocationUpdate = 'location_update';
  static const String socketEmergencyAlert = 'emergency_alert';
  static const String socketDestinationUpdate = 'destination_update';
  static const String socketStopSuggestion = 'stop_suggestion';
}

class AppStrings {
  // Authentication
  static const String loginTitle = 'Welcome to Astra';
  static const String loginSubtitle = 'Connect with your riding group';
  static const String registerTitle = 'Join Astra';
  static const String registerSubtitle = 'Start your group riding adventure';
  
  // Errors
  static const String networkError = 'Please check your internet connection';
  static const String locationError = 'Location access is required for group rides';
  static const String permissionError = 'Please grant required permissions';
  static const String genericError = 'Something went wrong. Please try again.';
  
  // Success Messages
  static const String groupCreated = 'Group created successfully!';
  static const String groupJoined = 'Joined group successfully!';
  static const String locationShared = 'Location shared with group';
}


pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  firebase_auth: ^4.15.3
  cloud_firestore: ^4.13.6
  google_maps_flutter: ^2.5.0
  location: ^5.0.3
  permission_handler: ^11.2.0
  socket_io_client: ^2.0.3+1
  provider: ^6.1.1
  shared_preferences: ^2.2.2
  uuid: ^4.3.3
  geolocator: ^10.1.0
  http: ^1.1.2

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0

app_config.dart
class AppConfig {
  // Environment
  static const bool isDevelopment = true;
  static const bool enableLogging = true;
  
  // Firebase Configuration (you'll add your config here)
  static const String firebaseWebApiKey = 'your-web-api-key';
  static const String firebaseAndroidAppId = 'your-android-app-id';
  static const String firebaseIosAppId = 'your-ios-app-id';
  static const String firebaseProjectId = 'astra-motorcycle-app';
  
  // Google Maps API Key (you'll need to get this)
  static const String googleMapsApiKey = 'your-google-maps-api-key';
  
  // Socket.IO Configuration (for future backend)
  static const String socketUrl = isDevelopment 
      ? 'http://localhost:3000' 
      : 'https://your-production-url.com';
  
  // Feature Flags
  static const bool enableRealTimeTracking = true;
  static const bool enableEmergencyAlerts = true;
  static const bool enableVoiceNavigation = false; // Future feature
  
  // App Settings
  static const Duration sessionTimeout = Duration(hours: 8);
  static const Duration locationUpdateInterval = Duration(seconds: 5);
  static const int maxRetryAttempts = 3;
}

main.dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/providers/group_provider.dart';
import 'package:astra/providers/location_provider.dart';
import 'package:astra/screens/auth/login_screen.dart';
import 'package:astra/screens/home/home_screen.dart';
import 'package:astra/config/theme.dart';
import 'package:astra/config/app_config.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Initialize Firebase
  await Firebase.initializeApp();
  
  runApp(const AstraApp());
}

class AstraApp extends StatelessWidget {
  const AstraApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => AuthProvider()),
        ChangeNotifierProvider(create: (_) => GroupProvider()),
        ChangeNotifierProvider(create: (_) => LocationProvider()),
      ],
      child: MaterialApp(
        title: 'Astra - Motorcycle Group Navigation',
        theme: AppTheme.lightTheme,
        darkTheme: AppTheme.darkTheme,
        themeMode: ThemeMode.system,
        home: const AuthWrapper(),
        debugShowCheckedModeBanner: AppConfig.isDevelopment,
      ),
    );
  }
}

class AuthWrapper extends StatelessWidget {
  const AuthWrapper({super.key});

  @override
  Widget build(BuildContext context) {
    return Consumer<AuthProvider>(
      builder: (context, authProvider, _) {
        // Show loading while checking authentication
        if (authProvider.isLoading) {
          return const Scaffold(
            body: Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  CircularProgressIndicator(),
                  SizedBox(height: 16),
                  Text('Loading Astra...'),
                ],
              ),
            ),
          );
        }
        
        // Show appropriate screen based on auth state
        return authProvider.isAuthenticated 
            ? const HomeScreen() 
            : const LoginScreen();
      },
    );
  }
}



models/user.dart
class User {
  final String id;
  final String name;
  final String email;
  final String? phoneNumber;
  final String? profileImageUrl;
  final String? fcmToken; // For push notifications
  final DateTime createdAt;
  final DateTime lastSeen;
  final bool isOnline;
  final RiderLocation? lastKnownLocation;

  User({
    required this.id,
    required this.name,
    required this.email,
    this.phoneNumber,
    this.profileImageUrl,
    this.fcmToken,
    required this.createdAt,
    required this.lastSeen,
    this.isOnline = false,
    this.lastKnownLocation,
  });

  // Convert from Firebase document
  factory User.fromMap(Map<String, dynamic> map, String id) {
    return User(
      id: id,
      name: map['name'] ?? '',
      email: map['email'] ?? '',
      phoneNumber: map['phoneNumber'],
      profileImageUrl: map['profileImageUrl'],
      fcmToken: map['fcmToken'],
      createdAt: DateTime.parse(map['createdAt']),
      lastSeen: DateTime.parse(map['lastSeen']),
      isOnline: map['isOnline'] ?? false,
      lastKnownLocation: map['lastKnownLocation'] != null
          ? RiderLocation.fromMap(map['lastKnownLocation'])
          : null,
    );
  }

  // Convert to Firebase document
  Map<String, dynamic> toMap() {
    return {
      'name': name,
      'email': email,
      'phoneNumber': phoneNumber,
      'profileImageUrl': profileImageUrl,
      'fcmToken': fcmToken,
      'createdAt': createdAt.toIso8601String(),
      'lastSeen': lastSeen.toIso8601String(),
      'isOnline': isOnline,
      'lastKnownLocation': lastKnownLocation?.toMap(),
    };
  }

  // Create copy with updated fields
  User copyWith({
    String? name,
    String? email,
    String? phoneNumber,
    String? profileImageUrl,
    String? fcmToken,
    DateTime? lastSeen,
    bool? isOnline,
    RiderLocation? lastKnownLocation,
  }) {
    return User(
      id: id,
      name: name ?? this.name,
      email: email ?? this.email,
      phoneNumber: phoneNumber ?? this.phoneNumber,
      profileImageUrl: profileImageUrl ?? this.profileImageUrl,
      fcmToken: fcmToken ?? this.fcmToken,
      createdAt: createdAt,
      lastSeen: lastSeen ?? this.lastSeen,
      isOnline: isOnline ?? this.isOnline,
      lastKnownLocation: lastKnownLocation ?? this.lastKnownLocation,
    );
  }

  // Helper methods
  String get displayName => name.isNotEmpty ? name : email.split('@').first;
  String get initials {
    List<String> names = name.split(' ');
    if (names.length >= 2) {
      return '${names[0][0]}${names[1][0]}'.toUpperCase();
    }
    return name.isNotEmpty ? name[0].toUpperCase() : email[0].toUpperCase();
  }

  @override
  String toString() {
    return 'User(id: $id, name: $name, email: $email, isOnline: $isOnline)';
  }
}



models/location.dart
enum LocationType {
  rider,        // Current rider position
  destination,  // Final destination
  stop,         // Approved stop point
  suggested,    // Suggested stop
  emergency,    // Emergency location
}

enum EmergencyType {
  breakdown,    // Mechanical issue
  accident,     // Accident occurred
  medical,      // Medical emergency
  fuel,         // Out of fuel
  weather,      // Weather-related stop
}

class RiderLocation {
  final String id;
  final double latitude;
  final double longitude;
  final double? altitude;
  final double? accuracy;
  final double? speed;
  final double? heading;
  final DateTime timestamp;
  final String? address;
  final LocationType type;
  final String? description;
  final String? userId;
  final EmergencyType? emergencyType;
  final bool isActive;

  RiderLocation({
    required this.id,
    required this.latitude,
    required this.longitude,
    this.altitude,
    this.accuracy,
    this.speed,
    this.heading,
    required this.timestamp,
    this.address,
    required this.type,
    this.description,
    this.userId,
    this.emergencyType,
    this.isActive = true,
  });

  factory RiderLocation.fromMap(Map<String, dynamic> map) {
    return RiderLocation(
      id: map['id'] ?? '',
      latitude: map['latitude']?.toDouble() ?? 0.0,
      longitude: map['longitude']?.toDouble() ?? 0.0,
      altitude: map['altitude']?.toDouble(),
      accuracy: map['accuracy']?.toDouble(),
      speed: map['speed']?.toDouble(),
      heading: map['heading']?.toDouble(),
      timestamp: DateTime.parse(map['timestamp']),
      address: map['address'],
      type: LocationType.values.firstWhere(
        (type) => type.name == map['type'],
        orElse: () => LocationType.rider,
      ),
      description: map['description'],
      userId: map['userId'],
      emergencyType: map['emergencyType'] != null
          ? EmergencyType.values.firstWhere(
              (type) => type.name == map['emergencyType'],
            )
          : null,
      isActive: map['isActive'] ?? true,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'latitude': latitude,
      'longitude': longitude,
      'altitude': altitude,
      'accuracy': accuracy,
      'speed': speed,
      'heading': heading,
      'timestamp': timestamp.toIso8601String(),
      'address': address,
      'type': type.name,
      'description': description,
      'userId': userId,
      'emergencyType': emergencyType?.name,
      'isActive': isActive,
    };
  }

  // Calculate distance to another location in meters
  double distanceTo(RiderLocation other) {
    const double earthRadius = 6371000; // Earth's radius in meters
    
    double lat1Rad = latitude * (3.14159265359 / 180);
    double lat2Rad = other.latitude * (3.14159265359 / 180);
    double deltaLatRad = (other.latitude - latitude) * (3.14159265359 / 180);
    double deltaLngRad = (other.longitude - longitude) * (3.14159265359 / 180);

    double a = (deltaLatRad / 2) * (deltaLatRad / 2) +
        lat1Rad * lat2Rad *
        (deltaLngRad / 2) * (deltaLngRad / 2);
    
    double c = 2 * (a * (1 - a));
    
    return earthRadius * c;
  }

  // Check if location is recent (within last 30 seconds)
  bool get isRecent {
    return DateTime.now().difference(timestamp).inSeconds < 30;
  }

  // Check if this is an emergency location
  bool get isEmergency => type == LocationType.emergency;

  // Get formatted speed
  String get speedKmh {
    if (speed == null) return 'N/A';
    return '${(speed! * 3.6).toStringAsFixed(1)} km/h';
  }

  // Get formatted address or coordinates
  String get displayLocation {
    return address ?? '${latitude.toStringAsFixed(6)}, ${longitude.toStringAsFixed(6)}';
  }

  RiderLocation copyWith({
    String? id,
    double? latitude,
    double? longitude,
    double? altitude,
    double? accuracy,
    double? speed,
    double? heading,
    DateTime? timestamp,
    String? address,
    LocationType? type,
    String? description,
    String? userId,
    EmergencyType? emergencyType,
    bool? isActive,
  }) {
    return RiderLocation(
      id: id ?? this.id,
      latitude: latitude ?? this.latitude,
      longitude: longitude ?? this.longitude,
      altitude: altitude ?? this.altitude,
      accuracy: accuracy ?? this.accuracy,
      speed: speed ?? this.speed,
      heading: heading ?? this.heading,
      timestamp: timestamp ?? this.timestamp,
      address: address ?? this.address,
      type: type ?? this.type,
      description: description ?? this.description,
      userId: userId ?? this.userId,
      emergencyType: emergencyType ?? this.emergencyType,
      isActive: isActive ?? this.isActive,
    );
  }

  @override
  String toString() {
    return 'RiderLocation(id: $id, lat: $latitude, lng: $longitude, type: ${type.name})';
  }
}


models/group.dart
import 'location.dart';
import 'user.dart';

enum GroupStatus {
  planning,    // Group created, planning route
  active,      // Currently riding
  paused,      // Stopped/break
  completed,   // Ride finished
  cancelled,   // Ride cancelled
}

enum MemberRole {
  leader,      // Can set destinations, approve stops
  member,      // Can suggest stops, send alerts
}

class GroupMember {
  final String userId;
  final String name;
  final MemberRole role;
  final DateTime joinedAt;
  final bool isOnline;
  final RiderLocation? currentLocation;
  final DateTime? lastLocationUpdate;

  GroupMember({
    required this.userId,
    required this.name,
    required this.role,
    required this.joinedAt,
    this.isOnline = false,
    this.currentLocation,
    this.lastLocationUpdate,
  });

  factory GroupMember.fromMap(Map<String, dynamic> map) {
    return GroupMember(
      userId: map['userId'],
      name: map['name'],
      role: MemberRole.values.firstWhere(
        (role) => role.name == map['role'],
        orElse: () => MemberRole.member,
      ),
      joinedAt: DateTime.parse(map['joinedAt']),
      isOnline: map['isOnline'] ?? false,
      currentLocation: map['currentLocation'] != null
          ? RiderLocation.fromMap(map['currentLocation'])
          : null,
      lastLocationUpdate: map['lastLocationUpdate'] != null
          ? DateTime.parse(map['lastLocationUpdate'])
          : null,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'userId': userId,
      'name': name,
      'role': role.name,
      'joinedAt': joinedAt.toIso8601String(),
      'isOnline': isOnline,
      'currentLocation': currentLocation?.toMap(),
      'lastLocationUpdate': lastLocationUpdate?.toIso8601String(),
    };
  }

  // Check if member's location is recent
  bool get hasRecentLocation {
    if (lastLocationUpdate == null) return false;
    return DateTime.now().difference(lastLocationUpdate!).inMinutes < 2;
  }

  GroupMember copyWith({
    String? name,
    MemberRole? role,
    bool? isOnline,
    RiderLocation? currentLocation,
    DateTime? lastLocationUpdate,
  }) {
    return GroupMember(
      userId: userId,
      name: name ?? this.name,
      role: role ?? this.role,
      joinedAt: joinedAt,
      isOnline: isOnline ?? this.isOnline,
      currentLocation: currentLocation ?? this.currentLocation,
      lastLocationUpdate: lastLocationUpdate ?? this.lastLocationUpdate,
    );
  }
}

class Group {
  final String id;
  final String name;
  final String description;
  final String leaderId;
  final List<GroupMember> members;
  final GroupStatus status;
  final RiderLocation? destination;
  final List<RiderLocation> suggestedStops;
  final List<RiderLocation> approvedStops;
  final DateTime createdAt;
  final DateTime? startedAt;
  final DateTime? completedAt;
  final String joinCode;
  final Map<String, dynamic>? rideStats; // Distance, duration, etc.

  Group({
    required this.id,
    required this.name,
    required this.description,
    required this.leaderId,
    required this.members,
    required this.status,
    this.destination,
    this.suggestedStops = const [],
    this.approvedStops = const [],
    required this.createdAt,
    this.startedAt,
    this.completedAt,
    required this.joinCode,
    this.rideStats,
  });

  factory Group.fromMap(Map<String, dynamic> map, String id) {
    return Group(
      id: id,
      name: map['name'],
      description: map['description'] ?? '',
      leaderId: map['leaderId'],
      members: (map['members'] as List?)
          ?.map((member) => GroupMember.fromMap(member))
          .toList() ?? [],
      status: GroupStatus.values.firstWhere(
        (status) => status.name == map['status'],
        orElse: () => GroupStatus.planning,
      ),
      destination: map['destination'] != null
          ? RiderLocation.fromMap(map['destination'])
          : null,
      suggestedStops: (map['suggestedStops'] as List?)
          ?.map((stop) => RiderLocation.fromMap(stop))
          .toList() ?? [],
      approvedStops: (map['approvedStops'] as List?)
          ?.map((stop) => RiderLocation.fromMap(stop))
          .toList() ?? [],
      createdAt: DateTime.parse(map['createdAt']),
      startedAt: map['startedAt'] != null 
          ? DateTime.parse(map['startedAt']) 
          : null,
      completedAt: map['completedAt'] != null 
          ? DateTime.parse(map['completedAt']) 
          : null,
      joinCode: map['joinCode'],
      rideStats: map['rideStats'],
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'name': name,
      'description': description,
      'leaderId': leaderId,
      'members': members.map((member) => member.toMap()).toList(),
      'status': status.name,
      'destination': destination?.toMap(),
      'suggestedStops': suggestedStops.map((stop) => stop.toMap()).toList(),
      'approvedStops': approvedStops.map((stop) => stop.toMap()).toList(),
      'createdAt': createdAt.toIso8601String(),
      'startedAt': startedAt?.toIso8601String(),
      'completedAt': completedAt?.toIso8601String(),
      'joinCode': joinCode,
      'rideStats': rideStats,
    };
  }

  // Helper methods
  bool get isActive => status == GroupStatus.active;
  bool get isPaused => status == GroupStatus.paused;
  bool get isCompleted => status == GroupStatus.completed;
  bool get isPlanning => status == GroupStatus.planning;
  
  int get memberCount => members.length;
  List<GroupMember> get onlineMembers => members.where((m) => m.isOnline).toList();
  List<GroupMember> get membersWithRecentLocation => members.where((m) => m.hasRecentLocation).toList();
  
  GroupMember? get leader => members.firstWhere(
    (member) => member.role == MemberRole.leader,
    orElse: () => members.first,
  );

  bool isUserLeader(String userId) {
    return leaderId == userId || members.any((m) => m.userId == userId && m.role == MemberRole.leader);
  }

  bool isUserMember(String userId) {
    return members.any((m) => m.userId == userId);
  }

  Duration? get rideDuration {
    if (startedAt == null) return null;
    DateTime endTime = completedAt ?? DateTime.now();
    return endTime.difference(startedAt!);
  }

  Group copyWith({
    String? name,
    String? description,
    List<GroupMember>? members,
    GroupStatus? status,
    RiderLocation? destination,
    List<RiderLocation>? suggestedStops,
    List<RiderLocation>? approvedStops,
    DateTime? startedAt,
    DateTime? completedAt,
    Map<String, dynamic>? rideStats,
  }) {
    return Group(
      id: id,
      name: name ?? this.name,
      description: description ?? this.description,
      leaderId: leaderId,
      members: members ?? this.members,
      status: status ?? this.status,
      destination: destination ?? this.destination,
      suggestedStops: suggestedStops ?? this.suggestedStops,
      approvedStops: approvedStops ?? this.approvedStops,
      createdAt: createdAt,
      startedAt: startedAt ?? this.startedAt,
      completedAt: completedAt ?? this.completedAt,
      joinCode: joinCode,
      rideStats: rideStats ?? this.rideStats,
    );
  }

  @override
  String toString() {
    return 'Group(id: $id, name: $name, members: ${members.length}, status: ${status.name})';
  }
}


models/ride.dart
import 'location.dart';
import 'group.dart';

enum RideType {
  casual,      // Casual group ride
  touring,     // Long distance touring
  commuting,   // Daily commute group
  adventure,   // Off-road adventure
  track,       // Track day
}

class RideWaypoint {
  final String id;
  final RiderLocation location;
  final String? name;
  final String? description;
  final DateTime? arrivalTime;
  final Duration? stopDuration;
  final bool isCompleted;

  RideWaypoint({
    required this.id,
    required this.location,
    this.name,
    this.description,
    this.arrivalTime,
    this.stopDuration,
    this.isCompleted = false,
  });

  factory RideWaypoint.fromMap(Map<String, dynamic> map) {
    return RideWaypoint(
      id: map['id'],
      location: RiderLocation.fromMap(map['location']),
      name: map['name'],
      description: map['description'],
      arrivalTime: map['arrivalTime'] != null 
          ? DateTime.parse(map['arrivalTime']) 
          : null,
      stopDuration: map['stopDuration'] != null 
          ? Duration(seconds: map['stopDuration']) 
          : null,
      isCompleted: map['isCompleted'] ?? false,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'location': location.toMap(),
      'name': name,
      'description': description,
      'arrivalTime': arrivalTime?.toIso8601String(),
      'stopDuration': stopDuration?.inSeconds,
      'isCompleted': isCompleted,
    };
  }
}

class RideStats {
  final double totalDistance; // in meters
  final Duration totalDuration;
  final Duration movingTime;
  final double averageSpeed; // in m/s
  final double maxSpeed; // in m/s
  final double totalElevationGain; // in meters
  final int numberOfStops;
  final List<RiderLocation> route;

  RideStats({
    required this.totalDistance,
    required this.totalDuration,
    required this.movingTime,
    required this.averageSpeed,
    required this.maxSpeed,
    required this.totalElevationGain,
    required this.numberOfStops,
    required this.route,
  });

  factory RideStats.fromMap(Map<String, dynamic> map) {
    return RideStats(
      totalDistance: map['totalDistance']?.toDouble() ?? 0.0,
      totalDuration: Duration(seconds: map['totalDuration'] ?? 0),
      movingTime: Duration(seconds: map['movingTime'] ?? 0),
      averageSpeed: map['averageSpeed']?.toDouble() ?? 0.0,
      maxSpeed: map['maxSpeed']?.toDouble() ?? 0.0,
      totalElevationGain: map['totalElevationGain']?.toDouble() ?? 0.0,
      numberOfStops: map['numberOfStops'] ?? 0,
      route: (map['route'] as List?)
          ?.map((location) => RiderLocation.fromMap(location))
          .toList() ?? [],
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'totalDistance': totalDistance,
      'totalDuration': totalDuration.inSeconds,
      'movingTime': movingTime.inSeconds,
      'averageSpeed': averageSpeed,
      'maxSpeed': maxSpeed,
      'totalElevationGain': totalElevationGain,
      'numberOfStops': numberOfStops,
      'route': route.map((location) => location.toMap()).toList(),
    };
  }

  // Helper getters
  String get totalDistanceKm => '${(totalDistance / 1000).toStringAsFixed(1)} km';
  String get averageSpeedKmh => '${(averageSpeed * 3.6).toStringAsFixed(1)} km/h';
  String get maxSpeedKmh => '${(maxSpeed * 3.6).toStringAsFixed(1)} km/h';
  String get formattedDuration {
    int hours = totalDuration.inHours;
    int minutes = totalDuration.inMinutes.remainder(60);
    return hours > 0 ? '${hours}h ${minutes}m' : '${minutes}m';
  }
}

class Ride {
  final String id;
  final String groupId;
  final String name;
  final String? description;
  final RideType type;
  final RiderLocation startLocation;
  final RiderLocation? endLocation;
  final List<RideWaypoint> waypoints;
  final DateTime createdAt;
  final DateTime? startedAt;
  final DateTime? completedAt;
  final RideStats? stats;
  final Map<String, List<RiderLocation>> memberPaths; // userId -> path
  final List<String> emergencyAlerts;
  final bool isPublic;

  Ride({
    required this.id,
    required this.groupId,
    required this.name,
    this.description,
    required this.type,
    required this.startLocation,
    this.endLocation,
    this.waypoints = const [],
    required this.createdAt,
    this.startedAt,
    this.completedAt,
    this.stats,
    this.memberPaths = const {},
    this.emergencyAlerts = const [],
    this.isPublic = false,
  });

  factory Ride.fromMap(Map<String, dynamic> map, String id) {
    return Ride(
      id: id,
      groupId: map['groupId'],
      name: map['name'],
      description: map['description'],
      type: RideType.values.firstWhere(
        (type) => type.name == map['type'],
        orElse: () => RideType.casual,
      ),
      startLocation: RiderLocation.fromMap(map['startLocation']),
      endLocation: map['endLocation'] != null 
          ? RiderLocation.fromMap(map['endLocation']) 
          : null,
      waypoints: (map['waypoints'] as List?)
          ?.map((waypoint) => RideWaypoint.fromMap(waypoint))
          .toList() ?? [],
      createdAt: DateTime.parse(map['createdAt']),
      startedAt: map['startedAt'] != null 
          ? DateTime.parse(map['startedAt']) 
          : null,
      completedAt: map['completedAt'] != null 
          ? DateTime.parse(map['completedAt']) 
          : null,
      stats: map['stats'] != null 
          ? RideStats.fromMap(map['stats']) 
          : null,
      memberPaths: Map<String, List<RiderLocation>>.from(
        (map['memberPaths'] as Map?)?.map((key, value) => 
          MapEntry(key, (value as List).map((location) => 
            RiderLocation.fromMap(location)).toList())) ?? {}),
      emergencyAlerts: List<String>.from(map['emergencyAlerts'] ?? []),
      isPublic: map['isPublic'] ?? false,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'groupId': groupId,
      'name': name,
      'description': description,
      'type': type.name,
      'startLocation': startLocation.toMap(),
      'endLocation': endLocation?.toMap(),
      'waypoints': waypoints.map((waypoint) => waypoint.toMap()).toList(),
      'createdAt': createdAt.toIso8601String(),
      'startedAt': startedAt?.toIso8601String(),
      'completedAt': completedAt?.toIso8601String(),
      'stats': stats?.toMap(),
      'memberPaths': memberPaths.map((key, value) => 
        MapEntry(key, value.map((location) => location.toMap()).toList())),
      'emergencyAlerts': emergencyAlerts,
      'isPublic': isPublic,
    };
  }

  // Helper methods
  bool get isActive => startedAt != null && completedAt == null;
  bool get isCompleted => completedAt != null;
  bool get hasEmergencies => emergencyAlerts.isNotEmpty;
  
  Duration? get duration {
    if (startedAt == null) return null;
    DateTime endTime = completedAt ?? DateTime.now();
    return endTime.difference(startedAt!);
  }

  @override
  String toString() {
    return 'Ride(id: $id, name: $name, type: ${type.name}, isActive: $isActive)';
  }
}

services/auth_service.dart
import 'package:firebase_auth/firebase_auth.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:astra/models/user.dart' as app_user;
import 'package:astra/utils/constants.dart';

class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;
  final FirebaseFirestore _firestore = FirebaseFirestore.instance;

  // Get current user
  User? get currentUser => _auth.currentUser;
  
  // Auth state stream
  Stream<User?> get authStateChanges => _auth.authStateChanges();

  // Check if user is signed in
  bool get isSignedIn => currentUser != null;

  // Sign up with email and password
  Future<app_user.User?> signUpWithEmailAndPassword({
    required String email,
    required String password,
    required String name,
    String? phoneNumber,
  }) async {
    try {
      // Create user with Firebase Auth
      UserCredential result = await _auth.createUserWithEmailAndPassword(
        email: email,
        password: password,
      );

      User? firebaseUser = result.user;
      if (firebaseUser == null) return null;

      // Update display name
      await firebaseUser.updateDisplayName(name);

      // Create user document in Firestore
      app_user.User newUser = app_user.User(
        id: firebaseUser.uid,
        name: name,
        email: email,
        phoneNumber: phoneNumber,
        createdAt: DateTime.now(),
        lastSeen: DateTime.now(),
        isOnline: true,
      );

      await _firestore
          .collection(AppConstants.usersCollection)
          .doc(firebaseUser.uid)
          .set(newUser.toMap());

      return newUser;
    } on FirebaseAuthException catch (e) {
      throw _handleAuthException(e);
    } catch (e) {
      throw Exception('Failed to create account: ${e.toString()}');
    }
  }

  // Sign in with email and password
  Future<app_user.User?> signInWithEmailAndPassword({
    required String email,
    required String password,
  }) async {
    try {
      UserCredential result = await _auth.signInWithEmailAndPassword(
        email: email,
        password: password,
      );

      User? firebaseUser = result.user;
      if (firebaseUser == null) return null;

      // Update user's online status and last seen
      await _updateUserOnlineStatus(firebaseUser.uid, true);

      // Get user data from Firestore
      return await getUserData(firebaseUser.uid);
    } on FirebaseAuthException catch (e) {
      throw _handleAuthException(e);
    } catch (e) {
      throw Exception('Failed to sign in: ${e.toString()}');
    }
  }

  // Sign out
  Future<void> signOut() async {
    try {
      if (currentUser != null) {
        // Update user's online status
        await _updateUserOnlineStatus(currentUser!.uid, false);
      }
      await _auth.signOut();
    } catch (e) {
      throw Exception('Failed to sign out: ${e.toString()}');
    }
  }

  // Get user data from Firestore
  Future<app_user.User?> getUserData(String userId) async {
    try {
      DocumentSnapshot doc = await _firestore
          .collection(AppConstants.usersCollection)
          .doc(userId)
          .get();

      if (doc.exists && doc.data() != null) {
        return app_user.User.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }
      return null;
    } catch (e) {
      throw Exception('Failed to get user data: ${e.toString()}');
    }
  }

  // Update user profile
  Future<void> updateUserProfile(app_user.User user) async {
    try {
      await _firestore
          .collection(AppConstants.usersCollection)
          .doc(user.id)
          .update(user.toMap());
    } catch (e) {
      throw Exception('Failed to update profile: ${e.toString()}');
    }
  }

  // Update user's online status
  Future<void> _updateUserOnlineStatus(String userId, bool isOnline) async {
    try {
      await _firestore
          .collection(AppConstants.usersCollection)
          .doc(userId)
          .update({
        'isOnline': isOnline,
        'lastSeen': DateTime.now().toIso8601String(),
      });
    } catch (e) {
      print('Failed to update online status: ${e.toString()}');
    }
  }

  // Reset password
  Future<void> resetPassword(String email) async {
    try {
      await _auth.sendPasswordResetEmail(email: email);
    } on FirebaseAuthException catch (e) {
      throw _handleAuthException(e);
    } catch (e) {
      throw Exception('Failed to send reset email: ${e.toString()}');
    }
  }

  // Delete account
  Future<void> deleteAccount() async {
    try {
      if (currentUser != null) {
        // Delete user document from Firestore
        await _firestore
            .collection(AppConstants.usersCollection)
            .doc(currentUser!.uid)
            .delete();

        // Delete Firebase Auth account
        await currentUser!.delete();
      }
    } catch (e) {
      throw Exception('Failed to delete account: ${e.toString()}');
    }
  }

  // Handle Firebase Auth exceptions
  String _handleAuthException(FirebaseAuthException e) {
    switch (e.code) {
      case 'weak-password':
        return 'The password provided is too weak.';
      case 'email-already-in-use':
        return 'An account already exists for this email.';
      case 'user-not-found':
        return 'No user found for this email.';
      case 'wrong-password':
        return 'Wrong password provided.';
      case 'invalid-email':
        return 'The email address is not valid.';
      case 'user-disabled':
        return 'This user account has been disabled.';
      case 'operation-not-allowed':
        return 'This operation is not allowed.';
      case 'too-many-requests':
        return 'Too many requests. Please try again later.';
      default:
        return 'Authentication failed: ${e.message}';
    }
  }

  // Check if email is verified
  bool get isEmailVerified => currentUser?.emailVerified ?? false;

  // Send email verification
  Future<void> sendEmailVerification() async {
    try {
      await currentUser?.sendEmailVerification();
    } catch (e) {
      throw Exception('Failed to send verification email: ${e.toString()}');
    }
  }

  // Reload current user
  Future<void> reloadUser() async {
    try {
      await currentUser?.reload();
    } catch (e) {
      throw Exception('Failed to reload user: ${e.toString()}');
    }
  }
}


services/location_service.dart
import 'dart:async';
import 'package:geolocator/geolocator.dart';
import 'package:permission_handler/permission_handler.dart';
import 'package:astra/models/location.dart';
import 'package:astra/utils/constants.dart';

class LocationService {
  static final LocationService _instance = LocationService._internal();
  factory LocationService() => _instance;
  LocationService._internal();

  StreamSubscription<Position>? _positionStream;
  StreamController<RiderLocation>? _locationController;
  RiderLocation? _lastKnownLocation;
  bool _isTracking = false;

  // Get location stream
  Stream<RiderLocation> get locationStream {
    _locationController ??= StreamController<RiderLocation>.broadcast();
    return _locationController!.stream;
  }

  // Get last known location
  RiderLocation? get lastKnownLocation => _lastKnownLocation;

  // Check if currently tracking
  bool get isTracking => _isTracking;

  // Request location permissions
  Future<bool> requestLocationPermission() async {
    try {
      // Check if location services are enabled
      bool serviceEnabled = await Geolocator.isLocationServiceEnabled();
      if (!serviceEnabled) {
        throw Exception('Location services are disabled. Please enable them in settings.');
      }

      // Check current permission status
      LocationPermission permission = await Geolocator.checkPermission();
      
      if (permission == LocationPermission.denied) {
        permission = await Geolocator.requestPermission();
        if (permission == LocationPermission.denied) {
          throw Exception('Location permissions are denied');
        }
      }
      
      if (permission == LocationPermission.deniedForever) {
        throw Exception('Location permissions are permanently denied. Please enable them in settings.');
      }

      // Request background location permission for continuous tracking
      if (permission == LocationPermission.whileInUse) {
        await Permission.locationAlways.request();
      }

      return true;
    } catch (e) {
      throw Exception('Failed to get location permission: ${e.toString()}');
    }
  }

  // Get current location once
  Future<RiderLocation> getCurrentLocation() async {
    try {
      bool hasPermission = await requestLocationPermission();
      if (!hasPermission) {
        throw Exception('Location permission denied');
      }

      Position position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high,
        timeLimit: Duration(seconds: AppConstants.locationTimeoutSeconds),
      );

      RiderLocation location = RiderLocation(
        id: DateTime.now().millisecondsSinceEpoch.toString(),
        latitude: position.latitude,
        longitude: position.longitude,
        altitude: position.altitude,
        accuracy: position.accuracy,
        speed: position.speed,
        heading: position.heading,
        timestamp: DateTime.now(),
        type: LocationType.rider,
      );

      _lastKnownLocation = location;
      return location;
    } catch (e) {
      throw Exception('Failed to get current location: ${e.toString()}');
    }
  }

  // Start continuous location tracking
  Future<void> startLocationTracking({String? userId}) async {
    try {
      if (_isTracking) return;

      bool hasPermission = await requestLocationPermission();
      if (!hasPermission) {
        throw Exception('Location permission denied');
      }

      _locationController ??= StreamController<RiderLocation>.broadcast();

      const LocationSettings locationSettings = LocationSettings(
        accuracy: LocationAccuracy.high,
        distanceFilter: AppConstants.minDistanceFilter.toInt(),
      );

      _positionStream = Geolocator.getPositionStream(
        locationSettings: locationSettings,
      ).listen(
        (Position position) {
          RiderLocation location = RiderLocation(
            id: DateTime.now().millisecondsSinceEpoch.toString(),
            latitude: position.latitude,
            longitude: position.longitude,
            altitude: position.altitude,
            accuracy: position.accuracy,
            speed: position.speed,
            heading: position.heading,
            timestamp: DateTime.now(),
            type: LocationType.rider,
            userId: userId,
          );

          _lastKnownLocation = location;
          _locationController?.add(location);
        },
        onError: (error) {
          print('Location tracking error: $error');
          _locationController?.addError(error);
        },
      );

      _isTracking = true;
    } catch (e) {
      throw Exception('Failed to start location tracking: ${e.toString()}');
    }
  }

  // Stop location tracking
  Future<void> stopLocationTracking() async {
    try {
      await _positionStream?.cancel();
      _positionStream = null;
      _isTracking = false;
    } catch (e) {
      print('Failed to stop location tracking: ${e.toString()}');
    }
  }

  // Calculate distance between two locations
  double calculateDistance(RiderLocation from, RiderLocation to) {
    return Geolocator.distanceBetween(
      from.latitude,
      from.longitude,
      to.latitude,
      to.longitude,
    );
  }

  // Calculate bearing between two locations
  double calculateBearing(RiderLocation from, RiderLocation to) {
    return Geolocator.bearingBetween(
      from.latitude,
      from.longitude,
      to.latitude,
      to.longitude,
    );
  }

  // Check if location is within a certain radius of another location
  bool isLocationNearby(RiderLocation location1, RiderLocation location2, double radiusInMeters) {
    double distance = calculateDistance(location1, location2);
    return distance <= radiusInMeters;
  }

  // Get location accuracy status
  String getLocationAccuracyStatus(double? accuracy) {
    if (accuracy == null) return 'Unknown';
    if (accuracy < 5) return 'Excellent';
    if (accuracy < 10) return 'Good';
    if (accuracy < 20) return 'Fair';
    return 'Poor';
  }

  // Check if location services are available
  Future<bool> isLocationServiceEnabled() async {
    return await Geolocator.isLocationServiceEnabled();
  }

  // Open location settings
  Future<void> openLocationSettings() async {
    await Geolocator.openLocationSettings();
  }

  // Dispose resources
  void dispose() {
    stopLocationTracking();
    _locationController?.close();
    _locationController = null;
  }
}


services/firebase_service.dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:astra/models/user.dart' as app_user;
import 'package:astra/models/group.dart';
import 'package:astra/models/ride.dart';
import 'package:astra/models/location.dart';
import 'package:astra/utils/constants.dart';

class FirebaseService {
  static final FirebaseService _instance = FirebaseService._internal();
  factory FirebaseService() => _instance;
  FirebaseService._internal();

  final FirebaseFirestore _firestore = FirebaseFirestore.instance;
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // Get current user ID
  String? get currentUserId => _auth.currentUser?.uid;

  // USERS COLLECTION METHODS

  // Create user document
  Future<void> createUser(app_user.User user) async {
    try {
      await _firestore
          .collection(AppConstants.usersCollection)
          .doc(user.id)
          .set(user.toMap());
    } catch (e) {
      throw Exception('Failed to create user: ${e.toString()}');
    }
  }

  // Get user by ID
  Future<app_user.User?> getUser(String userId) async {
    try {
      DocumentSnapshot doc = await _firestore
          .collection(AppConstants.usersCollection)
          .doc(userId)
          .get();

      if (doc.exists && doc.data() != null) {
        return app_user.User.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }
      return null;
    } catch (e) {
      throw Exception('Failed to get user: ${e.toString()}');
    }
  }

  // Update user
  Future<void> updateUser(app_user.User user) async {
    try {
      await _firestore
          .collection(AppConstants.usersCollection)
          .doc(user.id)
          .update(user.toMap());
    } catch (e) {
      throw Exception('Failed to update user: ${e.toString()}');
    }
  }

  // Get user stream
  Stream<app_user.User?> getUserStream(String userId) {
    return _firestore
        .collection(AppConstants.usersCollection)
        .doc(userId)
        .snapshots()
        .map((doc) {
      if (doc.exists && doc.data() != null) {
        return app_user.User.fromMap(doc.data()!, doc.id);
      }
      return null;
    });
  }

  // GROUPS COLLECTION METHODS

  // Create group
  Future<String> createGroup(Group group) async {
    try {
      DocumentReference docRef = await _firestore
          .collection(AppConstants.groupsCollection)
          .add(group.toMap());
      return docRef.id;
    } catch (e) {
      throw Exception('Failed to create group: ${e.toString()}');
    }
  }

  // Get group by ID
  Future<Group?> getGroup(String groupId) async {
    try {
      DocumentSnapshot doc = await _firestore
          .collection(AppConstants.groupsCollection)
          .doc(groupId)
          .get();

      if (doc.exists && doc.data() != null) {
        return Group.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }
      return null;
    } catch (e) {
      throw Exception('Failed to get group: ${e.toString()}');
    }
  }

  // Get group by join code
  Future<Group?> getGroupByJoinCode(String joinCode) async {
    try {
      QuerySnapshot query = await _firestore
          .collection(AppConstants.groupsCollection)
          .where('joinCode', isEqualTo: joinCode)
          .limit(1)
          .get();

      if (query.docs.isNotEmpty) {
        DocumentSnapshot doc = query.docs.first;
        return Group.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }
      return null;
    } catch (e) {
      throw Exception('Failed to find group: ${e.toString()}');
    }
  }

  // Update group
  Future<void> updateGroup(Group group) async {
    try {
      await _firestore
          .collection(AppConstants.groupsCollection)
          .doc(group.id)
          .update(group.toMap());
    } catch (e) {
      throw Exception('Failed to update group: ${e.toString()}');
    }
  }

  // Get group stream
  Stream<Group?> getGroupStream(String groupId) {
    return _firestore
        .collection(AppConstants.groupsCollection)
        .doc(groupId)
        .snapshots()
        .map((doc) {
      if (doc.exists && doc.data() != null) {
        return Group.fromMap(doc.data()!, doc.id);
      }
      return null;
    });
  }

  // Get user's groups
  Stream<List<Group>> getUserGroups(String userId) {
    return _firestore
        .collection(AppConstants.groupsCollection)
        .where('members', arrayContainsAny: [
          {'userId': userId}
        ])
        .snapshots()
        .map((snapshot) {
      return snapshot.docs.map((doc) {
        return Group.fromMap(doc.data(), doc.id);
      }).toList();
    });
  }

  // Add member to group
  Future<void> addMemberToGroup(String groupId, GroupMember member) async {
    try {
      await _firestore
          .collection(AppConstants.groupsCollection)
          .doc(groupId)
          .update({
        'members': FieldValue.arrayUnion([member.toMap()])
      });
    } catch (e) {
      throw Exception('Failed to add member to group: ${e.toString()}');
    }
  }

  // Remove member from group
  Future<void> removeMemberFromGroup(String groupId, String userId) async {
    try {
      Group? group = await getGroup(groupId);
      if (group != null) {
        List<GroupMember> updatedMembers = group.members
            .where((member) => member.userId != userId)
            .toList();
        
        await _firestore
            .collection(AppConstants.groupsCollection)
            .doc(groupId)
            .update({
          'members': updatedMembers.map((member) => member.toMap()).toList()
        });
      }
    } catch (e) {
      throw Exception('Failed to remove member from group: ${e.toString()}');
    }
  }

  // Update member location in group
  Future<void> updateMemberLocation(String groupId, String userId, RiderLocation location) async {
    try {
      Group? group = await getGroup(groupId);
      if (group != null) {
        List<GroupMember> updatedMembers = group.members.map((member) {
          if (member.userId == userId) {
            return member.copyWith(
              currentLocation: location,
              lastLocationUpdate: DateTime.now(),
              isOnline: true,
            );
          }
          return member;
        }).toList();
        
        await _firestore
            .collection(AppConstants.groupsCollection)
            .doc(groupId)
            .update({
          'members': updatedMembers.map((member) => member.toMap()).toList()
        });
      }
    } catch (e) {
      throw Exception('Failed to update member location: ${e.toString()}');
    }
  }

  // RIDES COLLECTION METHODS

  // Create ride
  Future<String> createRide(Ride ride) async {
    try {
      DocumentReference docRef = await _firestore
          .collection(AppConstants.ridesCollection)
          .add(ride.toMap());
      return docRef.id;
    } catch (e) {
      throw Exception('Failed to create ride: ${e.toString()}');
    }
  }

  // Get ride by ID
  Future<Ride?> getRide(String rideId) async {
    try {
      DocumentSnapshot doc = await _firestore
          .collection(AppConstants.ridesCollection)
          .doc(rideId)
          .get();

      if (doc.exists && doc.data() != null) {
        return Ride.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }
      return null;
    } catch (e) {
      throw Exception('Failed to get ride: ${e.toString()}');
    }
  }

  // Update ride
  Future<void> updateRide(Ride ride) async {
    try {
      await _firestore
          .collection(AppConstants.ridesCollection)
          .doc(ride.id)
          .update(ride.toMap());
    } catch (e) {
      throw Exception('Failed to update ride: ${e.toString()}');
    }
  }

  // Get group's rides
  Stream<List<Ride>> getGroupRides(String groupId) {
    return _firestore
        .collection(AppConstants.ridesCollection)
        .where('groupId', isEqualTo: groupId)
        .orderBy('createdAt', descending: true)
        .snapshots()
        .map((snapshot) {
      return snapshot.docs.map((doc) {
        return Ride.fromMap(doc.data(), doc.id);
      }).toList();
    });
  }

  // UTILITY METHODS

  // Delete document by ID
  Future<void> deleteDocument(String collection, String documentId) async {
    try {
      await _firestore.collection(collection).doc(documentId).delete();
    } catch (e) {
      throw Exception('Failed to delete document: ${e.toString()}');
    }
  }

  // Batch write operations
  Future<void> batchWrite(List<Map<String, dynamic>> operations) async {
    try {
      WriteBatch batch = _firestore.batch();
      
      for (Map<String, dynamic> operation in operations) {
        String type = operation['type'];
        String collection = operation['collection'];
        String? documentId = operation['documentId'];
        Map<String, dynamic>? data = operation['data'];
        
        DocumentReference docRef = documentId != null
            ? _firestore.collection(collection).doc(documentId)
            : _firestore.collection(collection).doc();
        
        switch (type) {
          case 'set':
            batch.set(docRef, data!);
            break;
          case 'update':
            batch.update(docRef, data!);
            break;
          case 'delete':
            batch.delete(docRef);
            break;
        }
      }
      
      await batch.commit();
    } catch (e) {
      throw Exception('Failed to execute batch write: ${e.toString()}');
    }
  }
}

services/group_service.dart
import 'dart:math';
import 'package:uuid/uuid.dart';
import 'package:astra/models/group.dart';
import 'package:astra/models/location.dart';
import 'package:astra/models/user.dart';
import 'package:astra/services/firebase_service.dart';
import 'package:astra/utils/constants.dart';

class GroupService {
  static final GroupService _instance = GroupService._internal();
  factory GroupService() => _instance;
  GroupService._internal();

  final FirebaseService _firebaseService = FirebaseService();
  final Uuid _uuid = const Uuid();

  // Generate unique join code
  String _generateJoinCode() {
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    Random random = Random();
    return String.fromCharCodes(Iterable.generate(
      AppConstants.groupCodeLength,
      (_) => chars.codeUnitAt(random.nextInt(chars.length))
    ));
  }

  // Create new group
  Future<Group> createGroup({
    required String name,
    required String description,
    required User leader,
    GroupStatus status = GroupStatus.planning,
  }) async {
    try {
      // Create leader as first member
      GroupMember leaderMember = GroupMember(
        userId: leader.id,
        name: leader.name,
        role: MemberRole.leader,
        joinedAt: DateTime.now(),
        isOnline: true,
      );

      // Create group object
      Group group = Group(
        id: '', // Will be set by Firebase
        name: name,
        description: description,
        leaderId: leader.id,
        members: [leaderMember],
        status: status,
        createdAt: DateTime.now(),
        joinCode: _generateJoinCode(),
      );

      // Save to Firebase and get ID
      String groupId = await _firebaseService.createGroup(group);
      
      // Return group with ID
      return group.copyWith().copyWith(/* id: groupId */); // Note: We need to modify copyWith to handle id
      
    } catch (e) {
      throw Exception('Failed to create group: ${e.toString()}');
    }
  }

  // Join group by code
  Future<Group> joinGroupByCode(String joinCode, User user) async {
    try {
      // Find group by join code
      Group? group = await _firebaseService.getGroupByJoinCode(joinCode);
      if (group == null) {
        throw Exception('Group not found. Please check the join code.');
      }

      // Check if user is already a member
      bool isAlreadyMember = group.members.any((member) => member.userId == user.id);
      if (isAlreadyMember) {
        throw Exception('You are already a member of this group.');
      }

      // Check if group is full
      if (group.members.length >= AppConstants.maxGroupMembers) {
        throw Exception('Group is full. Maximum ${AppConstants.maxGroupMembers} members allowed.');
      }

      // Create new member
      GroupMember newMember = GroupMember(
        userId: user.id,
        name: user.name,
        role: MemberRole.member,
        joinedAt: DateTime.now(),
        isOnline: true,
      );

      // Add member to group
      await _firebaseService.addMemberToGroup(group.id, newMember);

      // Return updated group
      List<GroupMember> updatedMembers = [...group.members, newMember];
      return group.copyWith(members: updatedMembers);
      
    } catch (e) {
      throw Exception('Failed to join group: ${e.toString()}');
    }
  }

  // Leave group
  Future<void> leaveGroup(String groupId, String userId) async {
    try {
      Group? group = await _firebaseService.getGroup(groupId);
      if (group == null) {
        throw Exception('Group not found.');
      }

      // Check if user is the leader
      if (group.leaderId == userId) {
        // If leader is leaving, assign leadership to another member or delete group
        if (group.members.length > 1) {
          // Find next member to be leader
          GroupMember? nextLeader = group.members.firstWhere(
            (member) => member.userId != userId,
            orElse: () => group.members.first,
          );
          
          if (nextLeader.userId != userId) {
            // Update group with new leader
            List<GroupMember> updatedMembers = group.members
                .where((member) => member.userId != userId)
                .map((member) => member.userId == nextLeader.userId
                    ? member.copyWith(role: MemberRole.leader)
                    : member)
                .toList();

            Group updatedGroup = group.copyWith(
              members: updatedMembers,
              // leaderId: nextLeader.userId, // Note: Need to update Group model to handle leaderId in copyWith
            );
            
            await _firebaseService.updateGroup(updatedGroup);
          }
        } else {
          // Last member leaving, delete group
          await _firebaseService.deleteDocument(AppConstants.groupsCollection, groupId);
        }
      } else {
        // Regular member leaving
        await _firebaseService.removeMemberFromGroup(groupId, userId);
      }
    } catch (e) {
      throw Exception('Failed to leave group: ${e.toString()}');
    }
  }

  // Update group destination
  Future<void> updateDestination(String groupId, RiderLocation destination, String userId) async {
    try {
      Group? group = await _firebaseService.getGroup(groupId);
      if (group == null) {
        throw Exception('Group not found.');
      }

      // Check if user is leader
      if (!group.isUserLeader(userId)) {
        throw Exception('Only group leaders can set destinations.');
      }

      // Update group with new destination
      Group updatedGroup = group.copyWith(destination: destination);
      await _firebaseService.updateGroup(updatedGroup);
      
    } catch (e) {
      throw Exception('Failed to update destination: ${e.toString()}');
    }
  }

  // Suggest stop
  Future<void> suggestStop(String groupId, RiderLocation stop, String userId) async {
    try {
      Group? group = await _firebaseService.getGroup(groupId);
      if (group == null) {
        throw Exception('Group not found.');
      }

      // Check if user is a member
      if (!group.isUserMember(userId)) {
        throw Exception('Only group members can suggest stops.');
      }

      // Add stop to suggested stops
      List<RiderLocation> updatedSuggestedStops = [...group.suggestedStops, stop];
      Group updatedGroup = group.copyWith(suggestedStops: updatedSuggestedStops);
      
      await _firebaseService.updateGroup(updatedGroup);
      
    } catch (e) {
      throw Exception('Failed to suggest stop: ${e.toString()}');
    }
  }

  // Approve stop (leader only)
  Future<void> approveStop(String groupId, RiderLocation stop, String userId) async {
    try {
      Group? group = await _firebaseService.getGroup(groupId);
      if (group == null) {
        throw Exception('Group not found')}
// Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can approve stops.');
     }

     // Remove from suggested stops and add to approved stops
     List<RiderLocation> updatedSuggestedStops = group.suggestedStops
         .where((suggestedStop) => suggestedStop.id != stop.id)
         .toList();
     
     List<RiderLocation> updatedApprovedStops = [...group.approvedStops, stop];
     
     Group updatedGroup = group.copyWith(
       suggestedStops: updatedSuggestedStops,
       approvedStops: updatedApprovedStops,
     );
     
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to approve stop: ${e.toString()}');
   }
 }

 // Reject stop (leader only)
 Future<void> rejectStop(String groupId, RiderLocation stop, String userId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can reject stops.');
     }

     // Remove from suggested stops
     List<RiderLocation> updatedSuggestedStops = group.suggestedStops
         .where((suggestedStop) => suggestedStop.id != stop.id)
         .toList();
     
     Group updatedGroup = group.copyWith(suggestedStops: updatedSuggestedStops);
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to reject stop: ${e.toString()}');
   }
 }

 // Start ride (leader only)
 Future<void> startRide(String groupId, String userId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can start rides.');
     }

     // Check if group has destination
     if (group.destination == null) {
       throw Exception('Please set a destination before starting the ride.');
     }

     // Update group status to active
     Group updatedGroup = group.copyWith(
       status: GroupStatus.active,
       startedAt: DateTime.now(),
     );
     
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to start ride: ${e.toString()}');
   }
 }

 // Pause ride (leader only)
 Future<void> pauseRide(String groupId, String userId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can pause rides.');
     }

     // Update group status to paused
     Group updatedGroup = group.copyWith(status: GroupStatus.paused);
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to pause ride: ${e.toString()}');
   }
 }

 // Resume ride (leader only)
 Future<void> resumeRide(String groupId, String userId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can resume rides.');
     }

     // Update group status to active
     Group updatedGroup = group.copyWith(status: GroupStatus.active);
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to resume ride: ${e.toString()}');
   }
 }

 // Complete ride (leader only)
 Future<void> completeRide(String groupId, String userId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is leader
     if (!group.isUserLeader(userId)) {
       throw Exception('Only group leaders can complete rides.');
     }

     // Update group status to completed
     Group updatedGroup = group.copyWith(
       status: GroupStatus.completed,
       completedAt: DateTime.now(),
     );
     
     await _firebaseService.updateGroup(updatedGroup);
     
   } catch (e) {
     throw Exception('Failed to complete ride: ${e.toString()}');
   }
 }

 // Update member location
 Future<void> updateMemberLocation(String groupId, String userId, RiderLocation location) async {
   try {
     await _firebaseService.updateMemberLocation(groupId, userId, location);
   } catch (e) {
     throw Exception('Failed to update member location: ${e.toString()}');
   }
 }

 // Send emergency alert
 Future<void> sendEmergencyAlert(String groupId, String userId, RiderLocation location, EmergencyType emergencyType) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Check if user is a member
     if (!group.isUserMember(userId)) {
       throw Exception('Only group members can send emergency alerts.');
     }

     // Create emergency location
     RiderLocation emergencyLocation = location.copyWith(
       type: LocationType.emergency,
       emergencyType: emergencyType,
       description: 'Emergency: ${emergencyType.name}',
     );

     // Update member location with emergency status
     await updateMemberLocation(groupId, userId, emergencyLocation);
     
     // TODO: Send push notifications to all group members
     // TODO: Integrate with emergency services if needed
     
   } catch (e) {
     throw Exception('Failed to send emergency alert: ${e.toString()}');
   }
 }

 // Get group statistics
 Future<Map<String, dynamic>> getGroupStatistics(String groupId) async {
   try {
     Group? group = await _firebaseService.getGroup(groupId);
     if (group == null) {
       throw Exception('Group not found.');
     }

     // Calculate basic statistics
     int totalMembers = group.members.length;
     int onlineMembers = group.onlineMembers.length;
     int membersWithLocation = group.membersWithRecentLocation.length;
     
     Duration? rideDuration = group.rideDuration;
     String durationText = rideDuration != null 
         ? '${rideDuration.inHours}h ${rideDuration.inMinutes.remainder(60)}m'
         : 'N/A';

     // Calculate distances if locations are available
     double totalDistance = 0.0;
     List<RiderLocation> memberLocations = group.members
         .where((member) => member.currentLocation != null)
         .map((member) => member.currentLocation!)
         .toList();

     if (memberLocations.length > 1) {
       // Calculate spread of group (distance between furthest members)
       double maxDistance = 0.0;
       for (int i = 0; i < memberLocations.length; i++) {
         for (int j = i + 1; j < memberLocations.length; j++) {
           double distance = memberLocations[i].distanceTo(memberLocations[j]);
           if (distance > maxDistance) {
             maxDistance = distance;
           }
         }
       }
       totalDistance = maxDistance;
     }

     return {
       'totalMembers': totalMembers,
       'onlineMembers': onlineMembers,
       'membersWithLocation': membersWithLocation,
       'rideDuration': durationText,
       'groupSpread': '${(totalDistance / 1000).toStringAsFixed(1)} km',
       'status': group.status.name,
       'hasDestination': group.destination != null,
       'suggestedStops': group.suggestedStops.length,
       'approvedStops': group.approvedStops.length,
       'createdAt': group.createdAt,
       'startedAt': group.startedAt,
       'completedAt': group.completedAt,
     };
   } catch (e) {
     throw Exception('Failed to get group statistics: ${e.toString()}');
   }
 }

 // Get nearby groups (for future feature)
 Future<List<Group>> getNearbyGroups(RiderLocation location, double radiusKm) async {
   try {
     // TODO: Implement geospatial query when Firebase supports it
     // For now, return empty list
     return [];
   } catch (e) {
     throw Exception('Failed to get nearby groups: ${e.toString()}');
   }
 }

 // Validate join code format
 bool isValidJoinCode(String code) {
   return code.length == AppConstants.groupCodeLength && 
          RegExp(r'^[A-Z0-9]+$').hasMatch(code);
 }

 // Check if user can perform action
 bool canUserPerformAction(Group group, String userId, String action) {
   bool isLeader = group.isUserLeader(userId);
   bool isMember = group.isUserMember(userId);
   
   switch (action) {
     case 'setDestination':
     case 'approveStop':
     case 'rejectStop':
     case 'startRide':
     case 'pauseRide':
     case 'resumeRide':
     case 'completeRide':
       return isLeader;
     case 'suggestStop':
     case 'sendEmergency':
     case 'updateLocation':
       return isMember;
     case 'leaveGroup':
       return isMember;
     default:
       return false;
   }
 }
}


services/map_service.dart
import 'dart:async';
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'package:google_maps_flutter/google_maps_flutter.dart';
import 'package:astra/models/location.dart';
import 'package:astra/config/app_config.dart';
import 'package:astra/config/theme.dart';

class MapService {
  static final MapService _instance = MapService._internal();
  factory MapService() => _instance;
  MapService._internal();

  // Google Maps API endpoints
  static const String _geocodingBaseUrl = 'https://maps.googleapis.com/maps/api/geocode/json';
  static const String _directionsBaseUrl = 'https://maps.googleapis.com/maps/api/directions/json';
  static const String _placesBaseUrl = 'https://maps.googleapis.com/maps/api/place';

  // Create markers for riders
  Set<Marker> createRiderMarkers(List<RiderLocation> locations, String currentUserId) {
    Set<Marker> markers = {};
    
    for (RiderLocation location in locations) {
      String markerId = location.userId ?? location.id;
      bool isCurrentUser = location.userId == currentUserId;
      bool isLeader = location.type == LocationType.rider && location.userId != null;
      bool isEmergency = location.type == LocationType.emergency;
      
      // Choose marker color based on status
      BitmapDescriptor markerIcon;
      if (isEmergency) {
        markerIcon = BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueRed);
      } else if (isCurrentUser) {
        markerIcon = BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueBlue);
      } else if (isLeader) {
        markerIcon = BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueOrange);
      } else {
        markerIcon = BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueGreen);
      }

      markers.add(
        Marker(
          markerId: MarkerId(markerId),
          position: LatLng(location.latitude, location.longitude),
          icon: markerIcon,
          infoWindow: InfoWindow(
            title: isCurrentUser ? 'You' : 'Rider',
            snippet: _getMarkerSnippet(location),
          ),
          rotation: location.heading ?? 0.0,
        ),
      );
    }
    
    return markers;
  }

  // Create markers for stops
  Set<Marker> createStopMarkers(List<RiderLocation> stops) {
    Set<Marker> markers = {};
    
    for (int i = 0; i < stops.length; i++) {
      RiderLocation stop = stops[i];
      
      markers.add(
        Marker(
          markerId: MarkerId('stop_${stop.id}'),
          position: LatLng(stop.latitude, stop.longitude),
          icon: BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueYellow),
          infoWindow: InfoWindow(
            title: 'Stop ${i + 1}',
            snippet: stop.description ?? stop.address ?? 'Planned stop',
          ),
        ),
      );
    }
    
    return markers;
  }

  // Create polyline for route
  Set<Polyline> createRoutePolylines(List<LatLng> routePoints) {
    if (routePoints.isEmpty) return {};
    
    return {
      Polyline(
        polylineId: const PolylineId('route'),
        points: routePoints,
        color: AppTheme.routeColor,
        width: 4,
        patterns: [],
      ),
    };
  }

  // Get directions between two points
  Future<List<LatLng>> getDirections(LatLng origin, LatLng destination) async {
    try {
      String url = '$_directionsBaseUrl?'
          'origin=${origin.latitude},${origin.longitude}&'
          'destination=${destination.latitude},${destination.longitude}&'
          'mode=driving&'
          'key=${AppConfig.googleMapsApiKey}';

      http.Response response = await http.get(Uri.parse(url));
      
      if (response.statusCode == 200) {
        Map<String, dynamic> data = json.decode(response.body);
        
        if (data['status'] == 'OK' && data['routes'].isNotEmpty) {
          String encodedPolyline = data['routes'][0]['overview_polyline']['points'];
          return _decodePolyline(encodedPolyline);
        } else {
          throw Exception('No route found: ${data['status']}');
        }
      } else {
        throw Exception('Failed to get directions: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Failed to get directions: ${e.toString()}');
    }
  }

  // Get directions with waypoints
  Future<List<LatLng>> getDirectionsWithWaypoints(
    LatLng origin,
    LatLng destination,
    List<LatLng> waypoints,
  ) async {
    try {
      String waypointsParam = waypoints
          .map((point) => '${point.latitude},${point.longitude}')
          .join('|');

      String url = '$_directionsBaseUrl?'
          'origin=${origin.latitude},${origin.longitude}&'
          'destination=${destination.latitude},${destination.longitude}&'
          'waypoints=$waypointsParam&'
          'mode=driving&'
          'key=${AppConfig.googleMapsApiKey}';

      http.Response response = await http.get(Uri.parse(url));
      
      if (response.statusCode == 200) {
        Map<String, dynamic> data = json.decode(response.body);
        
        if (data['status'] == 'OK' && data['routes'].isNotEmpty) {
          String encodedPolyline = data['routes'][0]['overview_polyline']['points'];
          return _decodePolyline(encodedPolyline);
        } else {
          throw Exception('No route found: ${data['status']}');
        }
      } else {
        throw Exception('Failed to get directions: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Failed to get directions with waypoints: ${e.toString()}');
    }
  }

  // Get address from coordinates (reverse geocoding)
  Future<String?> getAddressFromCoordinates(double latitude, double longitude) async {
    try {
      String url = '$_geocodingBaseUrl?'
          'latlng=$latitude,$longitude&'
          'key=${AppConfig.googleMapsApiKey}';

      http.Response response = await http.get(Uri.parse(url));
      
      if (response.statusCode == 200) {
        Map<String, dynamic> data = json.decode(response.body);
        
        if (data['status'] == 'OK' && data['results'].isNotEmpty) {
          return data['results'][0]['formatted_address'];
        }
      }
      return null;
    } catch (e) {
      print('Failed to get address: ${e.toString()}');
      return null;
    }
  }

  // Get coordinates from address (geocoding)
  Future<LatLng?> getCoordinatesFromAddress(String address) async {
    try {
      String url = '$_geocodingBaseUrl?'
          'address=${Uri.encodeComponent(address)}&'
          'key=${AppConfig.googleMapsApiKey}';

      http.Response response = await http.get(Uri.parse(url));
      
      if (response.statusCode == 200) {
        Map<String, dynamic> data = json.decode(response.body);
        
        if (data['status'] == 'OK' && data['results'].isNotEmpty) {
          Map<String, dynamic> location = data['results'][0]['geometry']['location'];
          return LatLng(location['lat'], location['lng']);
        }
      }
      return null;
    } catch (e) {
      print('Failed to get coordinates: ${e.toString()}');
      return null;
    }
  }

  // Calculate optimal camera position for multiple markers
  CameraPosition calculateOptimalCameraPosition(List<LatLng> positions) {
    if (positions.isEmpty) {
      return const CameraPosition(
        target: LatLng(0, 0),
        zoom: 10,
      );
    }

    if (positions.length == 1) {
      return CameraPosition(
        target: positions.first,
        zoom: 15,
      );
    }

    // Calculate bounds
    double minLat = positions.first.latitude;
    double maxLat = positions.first.latitude;
    double minLng = positions.first.longitude;
    double maxLng = positions.first.longitude;

    for (LatLng position in positions) {
      minLat = minLat < position.latitude ? minLat : position.latitude;
      maxLat = maxLat > position.latitude ? maxLat : position.latitude;
      minLng = minLng < position.longitude ? minLng : position.longitude;
      maxLng = maxLng > position.longitude ? maxLng : position.longitude;
    }

    // Calculate center
    double centerLat = (minLat + maxLat) / 2;
    double centerLng = (minLng + maxLng) / 2;

    // Calculate zoom level based on bounds
    double latDiff = maxLat - minLat;
    double lngDiff = maxLng - minLng;
    double maxDiff = latDiff > lngDiff ? latDiff : lngDiff;
    
    double zoom = 15.0;
    if (maxDiff > 0.01) zoom = 12.0;
    if (maxDiff > 0.05) zoom = 10.0;
    if (maxDiff > 0.1) zoom = 8.0;
    if (maxDiff > 0.5) zoom = 6.0;

    return CameraPosition(
      target: LatLng(centerLat, centerLng),
      zoom: zoom,
    );
  }

  // Helper method to get marker snippet
  String _getMarkerSnippet(RiderLocation location) {
    List<String> snippetParts = [];
    
    if (location.speed != null) {
      snippetParts.add(location.speedKmh);
    }
    
    if (location.isEmergency) {
      snippetParts.add('EMERGENCY: ${location.emergencyType?.name ?? 'Unknown'}');
    }
    
    if (location.address != null) {
      snippetParts.add(location.address!);
    }
    
    return snippetParts.join(' • ');
  }

  // Decode polyline from Google Directions API
  List<LatLng> _decodePolyline(String encoded) {
    List<LatLng> polylineCoordinates = [];
    int index = 0;
    int len = encoded.length;
    int lat = 0;
    int lng = 0;

    while (index < len) {
      int b;
      int shift = 0;
      int result = 0;
      do {
        b = encoded.codeUnitAt(index++) - 63;
        result |= (b & 0x1f) << shift;
        shift += 5;
      } while (b >= 0x20);
      int dlat = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
      lat += dlat;

      shift = 0;
      result = 0;
      do {
        b = encoded.codeUnitAt(index++) - 63;
        result |= (b & 0x1f) << shift;
        shift += 5;
      } while (b >= 0x20);
      int dlng = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
      lng += dlng;

      polylineCoordinates.add(LatLng(lat / 1E5, lng / 1E5));
    }

    return polylineCoordinates;
  }
}

providers/auth_provider.dart
import 'package:flutter/foundation.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:astra/models/user.dart' as app_user;
import 'package:astra/services/auth_service.dart';
import 'package:astra/utils/constants.dart';

enum AuthStatus {
  uninitialized,
  authenticated,
  unauthenticated,
  authenticating,
}

class AuthProvider extends ChangeNotifier {
  final AuthService _authService = AuthService();
  
  AuthStatus _status = AuthStatus.uninitialized;
  app_user.User? _currentUser;
  String? _errorMessage;
  bool _isLoading = false;

  // Getters
  AuthStatus get status => _status;
  app_user.User? get currentUser => _currentUser;
  String? get errorMessage => _errorMessage;
  bool get isLoading => _isLoading;
  bool get isAuthenticated => _status == AuthStatus.authenticated && _currentUser != null;
  String? get currentUserId => _currentUser?.id;

  AuthProvider() {
    _initializeAuth();
  }

  // Initialize authentication state
  void _initializeAuth() {
    _authService.authStateChanges.listen((User? firebaseUser) async {
      if (firebaseUser != null) {
        // User is signed in, get user data
        try {
          app_user.User? userData = await _authService.getUserData(firebaseUser.uid);
          if (userData != null) {
            _currentUser = userData;
            _status = AuthStatus.authenticated;
            _errorMessage = null;
          } else {
            // User exists in Firebase Auth but not in Firestore
            await signOut();
          }
        } catch (e) {
          _errorMessage = e.toString();
          _status = AuthStatus.unauthenticated;
        }
      } else {
        // User is signed out
        _currentUser = null;
        _status = AuthStatus.unauthenticated;
        _errorMessage = null;
      }
      notifyListeners();
    });
  }

  // Sign up with email and password
  Future<bool> signUp({
    required String email,
    required String password,
    required String name,
    String? phoneNumber,
  }) async {
    try {
      _setLoading(true);
      _clearError();

      // Validate inputs
      if (name.trim().isEmpty) {
        throw Exception('Name is required');
      }
      if (email.trim().isEmpty) {
        throw Exception('Email is required');
      }
      if (password.length < 6) {
        throw Exception('Password must be at least 6 characters');
      }

      _status = AuthStatus.authenticating;
      notifyListeners();

      // Create account
      app_user.User? user = await _authService.signUpWithEmailAndPassword(
        email: email.trim(),
        password: password,
        name: name.trim(),
        phoneNumber: phoneNumber?.trim(),
      );

      if (user != null) {
        _currentUser = user;
        _status = AuthStatus.authenticated;
        _setLoading(false);
        return true;
      } else {
        throw Exception('Failed to create account');
      }
    } catch (e) {
      _errorMessage = e.toString();
      _status = AuthStatus.unauthenticated;
      _setLoading(false);
      return false;
    }
  }

  // Sign in with email and password
  Future<bool> signIn({
    required String email,
    required String password,
  }) async {
    try {
      _setLoading(true);
      _clearError();

      // Validate inputs
      if (email.trim().isEmpty) {
        throw Exception('Email is required');
      }
      if (password.isEmpty) {
        throw Exception('Password is required');
      }

      _status = AuthStatus.authenticating;
      notifyListeners();

      // Sign in
      app_user.User? user = await _authService.signInWithEmailAndPassword(
        email: email.trim(),
        password: password,
      );

      if (user != null) {
        _currentUser = user;
        _status = AuthStatus.authenticated;
        _setLoading(false);
        return true;
      } else {
        throw Exception('Failed to sign in');
      }
    } catch (e) {
      _errorMessage = e.toString();
      _status = AuthStatus.unauthenticated;
      _setLoading(false);
      return false;
    }
  }

  // Sign out
  Future<void> signOut() async {
    try {
      _setLoading(true);
      await _authService.signOut();
      _currentUser = null;
      _status = AuthStatus.unauthenticated;
      _clearError();
    } catch (e) {
      _errorMessage = e.toString();
    } finally {
      _setLoading(false);
    }
  }

  // Update user profile
  Future<bool> updateProfile({
    String? name,
    String? phoneNumber,
    String? profileImageUrl,
  }) async {
    try {
      if (_currentUser == null) return false;

      _setLoading(true);
      _clearError();

      // Create updated user object
      app_user.User updatedUser = _currentUser!.copyWith(
        name: name ?? _currentUser!.name,
        phoneNumber: phoneNumber ?? _currentUser!.phoneNumber,
        profileImageUrl: profileImageUrl ?? _currentUser!.profileImageUrl,
      );

      // Update in Firebase
      await _authService.updateUserProfile(updatedUser);
      
      // Update local state
      _currentUser = updatedUser;
      _setLoading(false);
      
      return true;
    } catch (e) {
      _errorMessage = e.toString();
      _setLoading(false);
      return false;
    }
  }

  // Reset password
  Future<bool> resetPassword(String email) async {
    try {
      _setLoading(true);
      _clearError();

      if (email.trim().isEmpty) {
        throw Exception('Email is required');
      }

      await _authService.resetPassword(email.trim());
      _setLoading(false);
      return true;
    } catch (e) {
      _errorMessage = e.toString();
      _setLoading(false);
      return false;
    }
  }

  // Delete account
  Future<bool> deleteAccount() async {
    try {
      _setLoading(true);
      _clearError();

      await _authService.deleteAccount();
      
      _currentUser = null;
      _status = AuthStatus.unauthenticated;
      _setLoading(false);
      
      return true;
    } catch (e) {
      _errorMessage = e.toString();
      _setLoading(false);
      return false;
    }
  }

  // Send email verification
  Future<bool> sendEmailVerification() async {
    try {
      _setLoading(true);
      await _authService.sendEmailVerification();
      _setLoading(false);
      return true;
    } catch (e) {
      _errorMessage = e.toString();
      _setLoading(false);
      return false;
    }
  }

  // Check if email is verified
  bool get isEmailVerified => _authService.isEmailVerified;

  // Reload user data
  Future<void> reloadUser() async {
    try {
      if (_currentUser == null) return;
      
      await _authService.reloadUser();
      
      // Refresh user data
      app_user.User? refreshedUser = await _authService.getUserData(_currentUser!.id);
      if (refreshedUser != null) {
        _currentUser = refreshedUser;
        notifyListeners();
      }
    } catch (e) {
      _errorMessage = e.toString();
      notifyListeners();
    }
  }

  // Update user's online status
  Future<void> updateOnlineStatus(bool isOnline) async {
    try {
      if (_currentUser == null) return;

      app_user.User updatedUser = _currentUser!.copyWith(
        isOnline: isOnline,
        lastSeen: DateTime.now(),
      );

      await _authService.updateUserProfile(updatedUser);
      _currentUser = updatedUser;
      notifyListeners();
    } catch (e) {
      print('Failed to update online status: ${e.toString()}');
    }
  }

  // Validate email format
  bool isValidEmail(String email) {
    return RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(email);
  }

  // Validate password strength
  Map<String, bool> validatePassword(String password) {
    return {
      'minLength': password.length >= 6,
      'hasUppercase': password.contains(RegExp(r'[A-Z]')),
      'hasLowercase': password.contains(RegExp(r'[a-z]')),
      'hasNumbers': password.contains(RegExp(r'[0-9]')),
      'hasSpecialChars': password.contains(RegExp(r'[!@#$%^&*(),.?":{}|<>]')),
    };
  }

  // Get password strength score (0-5)
  int getPasswordStrength(String password) {
    Map<String, bool> validation = validatePassword(password);
    return validation.values.where((isValid) => isValid).length;
  }

  // Helper methods
  void _setLoading(bool loading) {
    _isLoading = loading;
    notifyListeners();
  }

  void _clearError() {
    _errorMessage = null;
    notifyListeners();
  }

  void clearError() {
    _clearError();
  }

  @override
  void dispose() {
    super.dispose();
  }
}


providers/location_provider.dart
import 'dart:async';
import 'package:flutter/foundation.dart';
import 'package:astra/models/location.dart';
import 'package:astra/services/location_service.dart';
import 'package:astra/utils/constants.dart';

enum LocationStatus {
  uninitialized,
  permissionDenied,
  serviceDisabled,
  tracking,
  stopped,
  error,
}

class LocationProvider extends ChangeNotifier {
  final LocationService _locationService = LocationService();
  
  LocationStatus _status = LocationStatus.uninitialized;
  RiderLocation? _currentLocation;
  List<RiderLocation> _locationHistory = [];
  String? _errorMessage;
  bool _isTracking = false;
  StreamSubscription<RiderLocation>? _locationSubscription;
  Timer? _locationTimer;

  // Getters
  LocationStatus get status => _status;
  RiderLocation? get currentLocation => _currentLocation;
  List<RiderLocation> get locationHistory => List.unmodifiableList(_locationHistory);
  String? get errorMessage => _errorMessage;
  bool get isTracking => _isTracking;
  bool get hasLocation => _currentLocation != null;
  bool get hasPermission => _status != LocationStatus.permissionDenied;
  bool get isServiceEnabled => _status != LocationStatus.serviceDisabled;

  // Location accuracy status
  String get accuracyStatus {
    if (_currentLocation?.accuracy == null) return 'Unknown';
    return _locationService.getLocationAccuracyStatus(_currentLocation!.accuracy);
  }

  // Current speed
  String get currentSpeed {
    return _currentLocation?.speedKmh ?? 'N/A';
  }

  // Distance traveled
  double get totalDistance {
    if (_locationHistory.length < 2) return 0.0;
    
    double total = 0.0;
    for (int i = 1; i < _locationHistory.length; i++) {
      total += _locationHistory[i-1].distanceTo(_locationHistory[i]);
    }
    return total;
  }

  // Formatted distance
  String get totalDistanceFormatted {
    double distance = totalDistance;
    if (distance < 1000) {
      return '${distance.toStringAsFixed(0)} m';
    } else {
      return '${(distance / 1000).toStringAsFixed(1)} km';
    }
  }

  // Initialize location services
  Future<void> initialize() async {
    try {
      _clearError();
      
      // Check if service is enabled
      bool serviceEnabled = await _locationService.isLocationServiceEnabled();
      if (!serviceEnabled) {
        _status = LocationStatus.serviceDisabled;
        _errorMessage = 'Location services are disabled';
        notifyListeners();
        return;
      }

      // Request permissions
      bool hasPermission = await _locationService.requestLocationPermission();
      if (!hasPermission) {
        _status = LocationStatus.permissionDenied;
        _errorMessage = 'Location permission denied';
        notifyListeners();
        return;
      }

      _status = LocationStatus.stopped;
      notifyListeners();
      
    } catch (e) {
      _status = LocationStatus.error;
      _errorMessage = e.toString();
      notifyListeners();
    }
  }

  // Get current location once
  Future<RiderLocation?> getCurrentLocation() async {
    try {
      _clearError();
      
      if (_status == LocationStatus.permissionDenied) {
        await initialize();
        if (_status == LocationStatus.permissionDenied) {
          return null;
        }
      }

      RiderLocation location = await _locationService.getCurrentLocation();
      _updateCurrentLocation(location);
      return location;
      
    } catch (e) {
      _status = LocationStatus.error;
      _errorMessage = e.toString();
      notifyListeners();
      return null;
    }
  }

  // Start continuous location tracking
  Future<bool> startTracking({String? userId}) async {
    try {
      if (_isTracking) return true;
      
      _clearError();
      
      // Ensure we have permissions
      if (_status == LocationStatus.uninitialized || 
          _status == LocationStatus.permissionDenied ||
          _status == LocationStatus.serviceDisabled) {
        await initialize();
        if (_status != LocationStatus.stopped) {
          return false;
        }
      }

      // Start location service
      await _locationService.startLocationTracking(userId: userId);
      
      // Subscribe to location updates
      _locationSubscription = _locationService.locationStream.listen(
        (RiderLocation location) {
          _updateCurrentLocation(location);
        },
        onError: (error) {
          _status = LocationStatus.error;
          _errorMessage = error.toString();
          notifyListeners();
        },
      );

      _isTracking = true;
      _status = LocationStatus.tracking;
      
      // Start periodic updates (backup for location stream)
      _startPeriodicUpdates(userId);
      
      notifyListeners();
      return true;
      
    } catch (e) {
      _status = LocationStatus.error;
      _errorMessage = e.toString();
      _isTracking = false;
      notifyListeners();
      return false;
    }
  }

  // Stop location tracking
  Future<void> stopTracking() async {
    try {
      await _locationService.stopLocationTracking();
      await _locationSubscription?.cancel();
      _locationSubscription = null;
      
      _locationTimer?.cancel();
      _locationTimer = null;
      
      _isTracking = false;
      _status = LocationStatus.stopped;
      
      notifyListeners();
      
    } catch (e) {
      _errorMessage = e.toString();
      notifyListeners();
    }
  }

  // Update current location
  void _updateCurrentLocation(RiderLocation location) {
    _currentLocation = location;
    
    // Add to history if it's a significant movement
    if (_locationHistory.isEmpty || 
        _locationHistory.last.distanceTo(location) > AppConstants.minDistanceFilter) {
      _locationHistory.add(location);
      
      // Keep history size manageable (last 1000 points)
      if (_locationHistory.length > 1000) {
        _locationHistory.removeAt(0);
      }
    }
    
    notifyListeners();
  }

  // Start periodic location updates (backup mechanism)
  void _startPeriodicUpdates(String? userId) {
    _locationTimer = Timer.periodic(
      Duration(seconds: AppConstants.locationUpdateInterval.toInt()),
      (timer) async {
        if (!_isTracking) {
          timer.cancel();
          return;
        }
        
        // This serves as a backup - the main updates come from the stream
        // But this ensures we don't miss updates due to stream issues
        try {
          if (_locationService.lastKnownLocation != null) {
            RiderLocation lastKnown = _locationService.lastKnownLocation!;
            if (_currentLocation == null || 
                _currentLocation!.timestamp.isBefore(lastKnown.timestamp)) {
              _updateCurrentLocation(lastKnown);
            }
          }
        } catch (e) {
          print('Periodic location update error: ${e.toString()}');
        }
      },
    );
  }

  // Calculate distance to a specific location
  double distanceTo(RiderLocation destination) {
    if (_currentLocation == null) return 0.0;
    return _currentLocation!.distanceTo(destination);
  }

  // Calculate bearing to a specific location
  double bearingTo(RiderLocation destination) {
    if (_currentLocation == null) return 0.0;
    return _locationService.calculateBearing(_currentLocation!, destination);
  }

  // Check if current location is near another location
  bool isNear(RiderLocation location, double radiusInMeters) {
    if (_currentLocation == null) return false;
    return _locationService.isLocationNearby(_currentLocation!, location, radiusInMeters);
  }

  // Get location history for a specific time range
  List<RiderLocation> getLocationHistory({
    DateTime? startTime,
    DateTime? endTime,
  }) {
    List<RiderLocation> filtered = _locationHistory;
    
    if (startTime != null) {
      filtered = filtered.where((loc) => loc.timestamp.isAfter(startTime)).toList();
    }
    
    if (endTime != null) {
      filtered = filtered.where((loc) => loc.timestamp.isBefore(endTime)).toList();
    }
    
    return filtered;
  }

  // Clear location history
  void clearHistory() {
    _locationHistory.clear();
    notifyListeners();
  }

  // Create emergency location
  RiderLocation? createEmergencyLocation(EmergencyType emergencyType, String description) {
    if (_currentLocation == null) return null;
    
    return _currentLocation!.copyWith(
      type: LocationType.emergency,
      emergencyType: emergencyType,
      description: description,
      timestamp: DateTime.now(),
    );
  }

  // Open location settings
  Future<void> openLocationSettings() async {
    await _locationService.openLocationSettings();
  }

  // Retry location initialization
  Future<void> retry() async {
    await initialize();
  }

  // Helper methods
  void _clearError() {
    _errorMessage = null;
  }

  void clearError() {
    _clearError();
    notifyListeners();
  }

  // Get location status message
  String getStatusMessage() {
    switch (_status) {
      case LocationStatus.uninitialized:
        return 'Initializing location services...';
      case LocationStatus.permissionDenied:
        return 'Location permission denied. Please enable in settings.';
      case LocationStatus.serviceDisabled:
        return 'Location services disabled. Please enable in settings.';
      case LocationStatus.tracking:
        return 'Location tracking active';
      case LocationStatus.stopped:
        return 'Location tracking stopped';
      case LocationStatus.error:
        return _errorMessage ?? 'Location error occurred';
    }
  }

  @override
  void dispose() {
    stopTracking();
    _locationTimer?.cancel();
    super.dispose();
  }
}

providers/group_provider.dart
import 'dart:async';
import 'package:flutter/foundation.dart';
import 'package:astra/models/group.dart';
import 'package:astra/models/location.dart';
import 'package:astra/models/user.dart';
import 'package:astra/services/group_service.dart';
import 'package:astra/services/firebase_service.dart';

enum GroupState {
  idle,
  loading,
  creating,
  joining,
  updating,
  error,
}

class GroupProvider extends ChangeNotifier {
  final GroupService _groupService = GroupService();
  final FirebaseService _firebaseService = FirebaseService();
  
  GroupState _state = GroupState.idle;
  Group? _currentGroup;
  List<Group> _userGroups = [];
  String? _errorMessage;
  StreamSubscription<Group?>? _groupSubscription;
  StreamSubscription<List<Group>>? _userGroupsSubscription;
  Map<String, dynamic>? _groupStatistics;

  // Getters
  GroupState get state => _state;
  Group? get currentGroup => _currentGroup;
  List<Group> get userGroups => List.unmodifiableList(_userGroups);
  String? get errorMessage => _errorMessage;
  bool get isLoading => _state == GroupState.loading || 
                        _state == GroupState.creating || 
                        _state == GroupState.joining ||
                        _state == GroupState.updating;
  bool get hasCurrentGroup => _currentGroup != null;
  bool get isInActiveGroup => _currentGroup?.isActive ?? false;
  bool get isGroupLeader => _currentGroup?.isUserLeader(_firebaseService.currentUserId ?? '') ?? false;
  Map<String, dynamic>? get groupStatistics => _groupStatistics;

  // Current group helpers
  int get currentGroupMemberCount => _currentGroup?.memberCount ?? 0;
  int get currentGroupOnlineCount => _currentGroup?.onlineMembers.length ?? 0;
  String get currentGroupStatus => _currentGroup?.status.name ?? 'unknown';
  bool get hasDestination => _currentGroup?.destination != null;
  int get suggestedStopsCount => _currentGroup?.suggestedStops.length ?? 0;
  int get approvedStopsCount => _currentGroup?.approvedStops.length ?? 0;

  // Create new group
  Future<bool> createGroup({
    required String name,
    required String description,
    required User leader,
  }) async {
    try {
      _setState(GroupState.creating);
      _clearError();

      // Validate inputs
      if (name.trim().isEmpty) {
        throw Exception('Group name is required');
      }
      if (leader.id.isEmpty) {
        throw Exception('Invalid user data');
      }

      // Create group
      Group group = await _groupService.createGroup(
        name: name.trim(),
        description: description.trim(),
        leader: leader,
      );

      // Set as current group and start listening
      await setCurrentGroup(group.id);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Join group by code
  Future<bool> joinGroup(String joinCode, User user) async {
    try {
      _setState(GroupState.joining);
      _clearError();

      // Validate join code
      if (!_groupService.isValidJoinCode(joinCode.toUpperCase())) {
        throw Exception('Invalid join code format');
      }

      // Join group
      Group group = await _groupService.joinGroupByCode(joinCode.toUpperCase(), user);
      
      // Set as current group and start listening
      await setCurrentGroup(group.id);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Set current group and start listening for updates
  Future<void> setCurrentGroup(String groupId) async {
    try {
      // Cancel existing subscription
      await _groupSubscription?.cancel();
      
      // Start listening to group updates
      _groupSubscription = _firebaseService.getGroupStream(groupId).listen(
        (Group? group) {
          _currentGroup = group;
          if (group != null) {
            _updateGroupStatistics();
          }
          notifyListeners();
        },
        onError: (error) {
          _errorMessage = error.toString();
          _setState(GroupState.error);
        },
      );
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
    }
  }

  // Load user's groups
  Future<void> loadUserGroups(String userId) async {
    try {
      // Cancel existing subscription
      await _userGroupsSubscription?.cancel();
      
      // Start listening to user's groups
      _userGroupsSubscription = _firebaseService.getUserGroups(userId).listen(
        (List<Group> groups) {
          _userGroups = groups;
          notifyListeners();
        },
        onError: (error) {
          print('Error loading user groups: ${error.toString()}');
        },
      );
      
    } catch (e) {
      print('Failed to load user groups: ${e.toString()}');
    }
  }

  // Leave current group
  Future<bool> leaveGroup(String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.leaveGroup(_currentGroup!.id, userId);
      
      // Clear current group
      _currentGroup = null;
      await _groupSubscription?.cancel();
      _groupSubscription = null;
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Update destination (leader only)
  Future<bool> updateDestination(RiderLocation destination, String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.updateDestination(_currentGroup!.id, destination, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Suggest stop
  Future<bool> suggestStop(RiderLocation stop, String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.suggestStop(_currentGroup!.id, stop, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Approve stop (leader only)
  Future<bool> approveStop(RiderLocation stop, String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.approveStop(_currentGroup!.id, stop, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Reject stop (leader only)
  Future<bool> rejectStop(RiderLocation stop, String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.rejectStop(_currentGroup!.id, stop, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Start ride (leader only)
  Future<bool> startRide(String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.startRide(_currentGroup!.id, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Pause ride (leader only)
  Future<bool> pauseRide(String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.pauseRide(_currentGroup!.id, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Resume ride (leader only)
  Future<bool> resumeRide(String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.resumeRide(_currentGroup!.id, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Complete ride (leader only)
  Future<bool> completeRide(String userId) async {
    try {
      if (_currentGroup == null) return false;
      
      _setState(GroupState.updating);
      _clearError();

      await _groupService.completeRide(_currentGroup!.id, userId);
      
      _setState(GroupState.idle);
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      _setState(GroupState.error);
      return false;
    }
  }

  // Update member location
  Future<void> updateMemberLocation(String userId, RiderLocation location) async {
    try {
      if (_currentGroup == null) return;
      
      await _groupService.updateMemberLocation(_currentGroup!.id, userId, location);
      
    } catch (e) {
      print('Failed to update member location: ${e.toString()}');
    }
  }

  // Send emergency alert
  Future<bool> sendEmergencyAlert(String userId, RiderLocation location, EmergencyType emergencyType) async {
    try {
      if (_currentGroup == null) return false;
      
      await _groupService.sendEmergencyAlert(_currentGroup!.id, userId, location, emergencyType);
      
      return true;
      
    } catch (e) {
      _errorMessage = e.toString();
      return false;
    }
  }

  // Update group statistics
  Future<void> _updateGroupStatistics() async {
    try {
      if (_currentGroup == null) return;
      
      _groupStatistics = await _groupService.getGroupStatistics(_currentGroup!.id);
      
    } catch (e) {
      print('Failed to update group statistics: ${e.toString()}');
    }
  }

  // Get member by user ID
  GroupMember? getMember(String userId) {
    return _currentGroup?.members.firstWhere(
      (member) => member.userId == userId,
      orElse: () => _currentGroup!.members.first,
    );
  }

  // Check if user can perform specific action
  bool canUserPerformAction(String userId, String action) {
    if (_currentGroup == null) return false;
    return _groupService.canUserPerformAction(_currentGroup!, userId, action);
  }

  // Get group member locations
  List<RiderLocation> getMemberLocations() {
    if (_currentGroup == null) return [];
    
    return _currentGroup!.members
        .where((member) => member.currentLocation != null)
        .map((member) => member.currentLocation!)
        .toList();
  }

  // Get emergency alerts
  List<GroupMember> getEmergencyAlerts() {
    if (_currentGroup == null) return [];
    
    return _currentGroup!.members
        .where((member) => member.currentLocation?.isEmergency ?? false)
        .toList();
  }

  // Search groups by name (for future feature)
  Future<List<Group>> searchGroups(String query) async {
    try {
      // TODO: Implement group search when backend supports it
      return [];
    } catch (e) {
      return [];
    }
  }

  // Get group activity summary
  Map<String, dynamic> getGroupActivitySummary() {
    if (_currentGroup == null) return {};
    
    DateTime now = DateTime.now();
    List<GroupMember> recentlyActive = _currentGroup!.members
        .where((member) => 
          member.lastLocationUpdate != null &&
          now.difference(member.lastLocationUpdate!).inMinutes < 10)
        .toList();
    
    return {
      'totalMembers': _currentGroup!.memberCount,
      'onlineMembers': _currentGroup!.onlineMembers.length,
      'recentlyActive': recentlyActive.length,
      'hasEmergencies': getEmergencyAlerts().isNotEmpty,
      'emergencyCount': getEmergencyAlerts().length,
      'status': _currentGroup!.status.name,
      'hasDestination': _currentGroup!.destination != null,
      'stopsSuggested': _currentGroup!.suggestedStops.length,
      'stopsApproved': _currentGroup!.approvedStops.length,
      'rideDuration': _currentGroup!.rideDuration?.inMinutes ?? 0,
    };
  }

  // Filter groups by status
  List<Group> getGroupsByStatus(GroupStatus status) {
    return _userGroups.where((group) => group.status == status).toList();
  }

  // Get active groups
  List<Group> get activeGroups => getGroupsByStatus(GroupStatus.active);
  
  // Get completed groups
  List<Group> get completedGroups => getGroupsByStatus(GroupStatus.completed);
  
  // Get planning groups
  List<Group> get planningGroups => getGroupsByStatus(GroupStatus.planning);

  // Clear current group
  void clearCurrentGroup() {
    _currentGroup = null;
    _groupSubscription?.cancel();
    _groupSubscription = null;
    _groupStatistics = null;
    notifyListeners();
  }

  // Refresh current group
  Future<void> refreshCurrentGroup() async {
    if (_currentGroup != null) {
      await setCurrentGroup(_currentGroup!.id);
    }
  }

  // Helper methods
  void _setState(GroupState newState) {
    _state = newState;
    notifyListeners();
  }

  void _clearError() {
    _errorMessage = null;
  }

  void clearError() {
    _clearError();
    notifyListeners();
  }

  // Get state message for UI
  String getStateMessage() {
    switch (_state) {
      case GroupState.idle:
        return '';
      case GroupState.loading:
        return 'Loading...';
      case GroupState.creating:
        return 'Creating group...';
      case GroupState.joining:
        return 'Joining group...';
      case GroupState.updating:
        return 'Updating...';
      case GroupState.error:
        return _errorMessage ?? 'An error occurred';
    }
  }

  @override
  void dispose() {
    _groupSubscription?.cancel();
    _userGroupsSubscription?.cancel();
    super.dispose();
  }
}



config/theme.dart
import 'package:flutter/material.dart';

class AppTheme {
  // Colors
  static const Color primaryColor = Color(0xFF2196F3); // Blue
  static const Color accentColor = Color(0xFFFF5722);  // Orange
  static const Color backgroundColor = Color(0xFFF5F5F5);
  static const Color cardColor = Color(0xFFFFFFFF);
  static const Color textPrimary = Color(0xFF212121);
  static const Color textSecondary = Color(0xFF757575);
  static const Color errorColor = Color(0xFFD32F2F);
  static const Color successColor = Color(0xFF388E3C);
  
  // Motorcycle-friendly colors for map
  static const Color riderColor = Color(0xFF4CAF50);    // Green for riders
  static const Color leaderColor = Color(0xFFFF9800);   // Orange for leader
  static const Color routeColor = Color(0xFF2196F3);    // Blue for route
  static const Color stopColor = Color(0xFFF44336);     // Red for stops
  static const Color emergencyColor = Color(0xFFE91E63); // Pink for emergency

  static ThemeData get lightTheme {
    return ThemeData(
      useMaterial3: true,
      primarySwatch: Colors.blue,
      primaryColor: primaryColor,
      scaffoldBackgroundColor: backgroundColor,
      cardColor: cardColor,
      
      // App Bar Theme
      appBarTheme: const AppBarTheme(
        backgroundColor: primaryColor,
        foregroundColor: Colors.white,
        elevation: 2,
        centerTitle: true,
        titleTextStyle: TextStyle(
          fontSize: 20,
          fontWeight: FontWeight.bold,
          color: Colors.white,
        ),
      ),
      
      // Button Themes
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          backgroundColor: primaryColor,
          foregroundColor: Colors.white,
          padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 12),
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(8),
          ),
          textStyle: const TextStyle(
            fontSize: 16,
            fontWeight: FontWeight.w600,
          ),
        ),
      ),
      
      // Input Field Theme
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Colors.white,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: Colors.grey),
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: Colors.grey),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: primaryColor, width: 2),
        ),
        contentPadding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
      ),
      
      // Card Theme
      cardTheme: CardTheme(
        color: cardColor,
        elevation: 2,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
        ),
      ),
      
      // Text Themes
      textTheme: const TextTheme(
        headlineLarge: TextStyle(
          fontSize: 28,
          fontWeight: FontWeight.bold,
          color: textPrimary,
        ),
        headlineMedium: TextStyle(
          fontSize: 24,
          fontWeight: FontWeight.w600,
          color: textPrimary,
        ),
        bodyLarge: TextStyle(
          fontSize: 16,
          color: textPrimary,
        ),
        bodyMedium: TextStyle(
          fontSize: 14,
          color: textSecondary,
        ),
      ),
      
      // Bottom Navigation Theme
      bottomNavigationBarTheme: const BottomNavigationBarThemeData(
        backgroundColor: Colors.white,
        selectedItemColor: primaryColor,
        unselectedItemColor: textSecondary,
        type: BottomNavigationBarType.fixed,
        elevation: 8,
      ),
    );
  }

  static ThemeData get darkTheme {
    return lightTheme.copyWith(
      brightness: Brightness.dark,
      scaffoldBackgroundColor: const Color(0xFF121212),
      cardColor: const Color(0xFF1E1E1E),
      
      appBarTheme: lightTheme.appBarTheme.copyWith(
        backgroundColor: const Color(0xFF1E1E1E),
      ),
      
      bottomNavigationBarTheme: lightTheme.bottomNavigationBarTheme.copyWith(
        backgroundColor: const Color(0xFF1E1E1E),
      ),
      
      textTheme: lightTheme.textTheme.copyWith(
        headlineLarge: lightTheme.textTheme.headlineLarge?.copyWith(
          color: Colors.white,
        ),
        headlineMedium: lightTheme.textTheme.headlineMedium?.copyWith(
          color: Colors.white,
        ),
        bodyLarge: lightTheme.textTheme.bodyLarge?.copyWith(
          color: Colors.white,
        ),
      ),
    );
  }
}

screens/auth/login_screen.dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/screens/auth/register_screen.dart';
import 'package:astra/utils/constants.dart';
import 'package:astra/widgets/common/custom_button.dart';
import 'package:astra/widgets/common/custom_text_field.dart';
import 'package:astra/widgets/common/loading_widget.dart';

class LoginScreen extends StatefulWidget {
  const LoginScreen({super.key});

  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  bool _obscurePassword = true;
  bool _rememberMe = false;

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Consumer<AuthProvider>(
          builder: (context, authProvider, child) {
            return Stack(
              children: [
                // Background gradient
                Container(
                  decoration: const BoxDecoration(
                    gradient: LinearGradient(
                      begin: Alignment.topCenter,
                      end: Alignment.bottomCenter,
                      colors: [
                        Color(0xFF2196F3),
                        Color(0xFF1976D2),
                      ],
                    ),
                  ),
                ),
                
                // Main content
                SingleChildScrollView(
                  padding: const EdgeInsets.all(24.0),
                  child: Column(
                    children: [
                      const SizedBox(height: 60),
                      
                      // App Logo and Title
                      _buildHeader(),
                      
                      const SizedBox(height: 60),
                      
                      // Login Form
                      _buildLoginForm(authProvider),
                      
                      const SizedBox(height: 24),
                      
                      // Error Message
                      if (authProvider.errorMessage != null)
                        _buildErrorMessage(authProvider.errorMessage!),
                      
                      const SizedBox(height: 24),
                      
                      // Sign Up Link
                      _buildSignUpLink(),
                    ],
                  ),
                ),
                
                // Loading Overlay
                if (authProvider.isLoading)
                  const LoadingWidget(message: 'Signing in...'),
              ],
            );
          },
        ),
      ),
    );
  }

  Widget _buildHeader() {
    return Column(
      children: [
        // App Icon
        Container(
          width: 80,
          height: 80,
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.circular(20),
            boxShadow: [
              BoxShadow(
                color: Colors.black.withOpacity(0.1),
                blurRadius: 10,
                offset: const Offset(0, 5),
              ),
            ],
          ),
          child: const Icon(
            Icons.two_wheeler,
            size: 40,
            color: Color(0xFF2196F3),
          ),
        ),
        
        const SizedBox(height: 20),
        
        // Title and Subtitle
        const Text(
          AppStrings.loginTitle,
          style: TextStyle(
            fontSize: 28,
            fontWeight: FontWeight.bold,
            color: Colors.white,
          ),
        ),
        
        const SizedBox(height: 8),
        
        const Text(
          AppStrings.loginSubtitle,
          style: TextStyle(
            fontSize: 16,
            color: Colors.white70,
          ),
        ),
      ],
    );
  }

  Widget _buildLoginForm(AuthProvider authProvider) {
    return Card(
      elevation: 8,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(16),
      ),
      child: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              // Email Field
              CustomTextField(
                controller: _emailController,
                label: 'Email',
                hint: 'Enter your email',
                keyboardType: TextInputType.emailAddress,
                prefixIcon: Icons.email_outlined,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Email is required';
                  }
                  if (!authProvider.isValidEmail(value)) {
                    return 'Please enter a valid email';
                  }
                  return null;
                },
              ),
              
              const SizedBox(height: 16),
              
              // Password Field
              CustomTextField(
                controller: _passwordController,
                label: 'Password',
                hint: 'Enter your password',
                obscureText: _obscurePassword,
                prefixIcon: Icons.lock_outlined,
                suffixIcon: IconButton(
                  icon: Icon(
                    _obscurePassword ? Icons.visibility : Icons.visibility_off,
                  ),
                  onPressed: () {
                    setState(() {
                      _obscurePassword = !_obscurePassword;
                    });
                  },
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Password is required';
                  }
                  return null;
                },
              ),
              
              const SizedBox(height: 16),
              
              // Remember Me & Forgot Password
              Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  Row(
                    children: [
                      Checkbox(
                        value: _rememberMe,
                        onChanged: (value) {
                          setState(() {
                            _rememberMe = value ?? false;
                          });
                        },
                      ),
                      const Text('Remember me'),
                    ],
                  ),
                  TextButton(
                    onPressed: () => _showForgotPasswordDialog(authProvider),
                    child: const Text('Forgot Password?'),
                  ),
                ],
              ),
              
              const SizedBox(height: 24),
              
              // Login Button
              CustomButton(
                text: 'Sign In',
                onPressed: () => _handleLogin(authProvider),
                isLoading: authProvider.isLoading,
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildErrorMessage(String message) {
    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: Colors.red.shade50,
        borderRadius: BorderRadius.circular(8),
        border: Border.all(color: Colors.red.shade200),
      ),
      child: Row(
        children: [
          Icon(Icons.error_outline, color: Colors.red.shade600),
          const SizedBox(width: 8),
          Expanded(
            child: Text(
              message,
              style: TextStyle(
                color: Colors.red.shade600,
                fontSize: 14,
              ),
            ),
          ),
        ],
      ),
    );
  }

  Widget _buildSignUpLink() {
    return Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        const Text(
          "Don't have an account? ",
          style: TextStyle(color: Colors.white70),
        ),
        TextButton(
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => const RegisterScreen()),
            );
          },
          child: const Text(
            'Sign Up',
            style: TextStyle(
              color: Colors.white,
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
      ],
    );
  }

  Future<void> _handleLogin(AuthProvider authProvider) async {
    if (!_formKey.currentState!.validate()) return;

    // Clear any previous errors
    authProvider.clearError();

    bool success = await authProvider.signIn(
      email: _emailController.text.trim(),
      password: _passwordController.text,
    );

    if (success) {
      // Navigation is handled by AuthWrapper in main.dart
    } else {
      // Error is already set in provider, UI will show it
    }
  }

  void _showForgotPasswordDialog(AuthProvider authProvider) {
    final emailController = TextEditingController();
    
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Reset Password'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text('Enter your email to receive a password reset link:'),
            const SizedBox(height: 16),
            CustomTextField(
              controller: emailController,
              label: 'Email',
              hint: 'Enter your email',
              keyboardType: TextInputType.emailAddress,
              prefixIcon: Icons.email_outlined,
            ),
          ],
        ),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: const Text('Cancel'),
          ),
          ElevatedButton(
            onPressed: () async {
              if (emailController.text.trim().isNotEmpty) {
                Navigator.pop(context);
                bool success = await authProvider.resetPassword(
                  emailController.text.trim(),
                );
                
                if (success && mounted) {
                  ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(
                      content: Text('Password reset email sent!'),
                      backgroundColor: Colors.green,
                    ),
                  );
                }
              }
            },
            child: const Text('Send'),
          ),
        ],
      ),
    );
  }
}

screens/auth/register_screen.dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/utils/constants.dart';
import 'package:astra/widgets/common/custom_button.dart';
import 'package:astra/widgets/common/custom_text_field.dart';
import 'package:astra/widgets/common/loading_widget.dart';

class RegisterScreen extends StatefulWidget {
  const RegisterScreen({super.key});

  @override
  State<RegisterScreen> createState() => _RegisterScreenState();
}

class _RegisterScreenState extends State<RegisterScreen> {
  final _formKey = GlobalKey<FormState>();
  final _nameController = TextEditingController();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final _confirmPasswordController = TextEditingController();
  final _phoneController = TextEditingController();
  bool _obscurePassword = true;
  bool _obscureConfirmPassword = true;
  bool _acceptTerms = false;

  @override
  void dispose() {
    _nameController.dispose();
    _emailController.dispose();
    _passwordController.dispose();
    _confirmPasswordController.dispose();
    _phoneController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Consumer<AuthProvider>(
          builder: (context, authProvider, child) {
            return Stack(
              children: [
                // Background gradient
                Container(
                  decoration: const BoxDecoration(
                    gradient: LinearGradient(
                      begin: Alignment.topCenter,
                      end: Alignment.bottomCenter,
                      colors: [
                        Color(0xFF2196F3),
                        Color(0xFF1976D2),
                      ],
                    ),
                  ),
                ),
                
                // Main content
                SingleChildScrollView(
                  padding: const EdgeInsets.all(24.0),
                  child: Column(
                    children: [
                      const SizedBox(height: 40),
                      
                      // Header
                      _buildHeader(),
                      
                      const SizedBox(height: 40),
                      
                      // Registration Form
                      _buildRegistrationForm(authProvider),
                      
                      const SizedBox(height: 24),
                      
                      // Error Message
                      if (authProvider.errorMessage != null)
                        _buildErrorMessage(authProvider.errorMessage!),
                      
                      const SizedBox(height: 24),
                      
                      // Sign In Link
                      _buildSignInLink(),
                    ],
                  ),
                ),
                
                // Loading Overlay
                if (authProvider.isLoading)
                  const LoadingWidget(message: 'Creating account...'),
              ],
            );
          },
        ),
      ),
    );
  }

  Widget _buildHeader() {
    return Column(
      children: [
        // Back Button
        Align(
          alignment: Alignment.centerLeft,
          child: IconButton(
            onPressed: () => Navigator.pop(context),
            icon: const Icon(
              Icons.arrow_back,
              color: Colors.white,
            ),
          ),
        ),
        
        const SizedBox(height: 20),
        
        // Title and Subtitle
        const Text(
          AppStrings.registerTitle,
          style: TextStyle(
            fontSize: 28,
            fontWeight: FontWeight.bold,
            color: Colors.white,
          ),
        ),
        
        const SizedBox(height: 8),
        
        const Text(
          AppStrings.registerSubtitle,
          style: TextStyle(
            fontSize: 16,
            color: Colors.white70,
          ),
        ),
      ],
    );
  }

  Widget _buildRegistrationForm(AuthProvider authProvider) {
    return Card(
      elevation: 8,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(16),
      ),
      child: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              // Name Field
              CustomTextField(
                controller: _nameController,
                label: 'Full Name',
                hint: 'Enter your full name',
                keyboardType: TextInputType.name,
                prefixIcon: Icons.person_outlined,
                validator: (value) {
                  if (value == null || value.trim().isEmpty) {
                    return 'Name is required';
                  }
                  if (value.trim().length < 2) {
                    return 'Name must be at least 2 characters';
                  }
                  return null;
                },
              ),
              
              const SizedBox(height: 16),
              
              // Email Field
              CustomTextField(
                controller: _emailController,
                label: 'Email',
                hint: 'Enter your email',
                keyboardType: TextInputType.emailAddress,
                prefixIcon: Icons.email_outlined,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Email is required';
                  }
                  if (!authProvider.isValidEmail(value)) {
                    return 'Please enter a valid email';
                  }
                  return null;
                },
              ),
              
              const SizedBox(height: 16),
              
              // Phone Field (Optional)
              CustomTextField(
                controller: _phoneController,
                label: 'Phone Number (Optional)',
                hint: 'Enter your phone number',
                keyboardType: TextInputType.phone,
                prefixIcon: Icons.phone_outlined,
              ),
              
              const SizedBox(height: 16),
              
              // Password Field
              CustomTextField(
                controller: _passwordController,
                label: 'Password',
                hint: 'Enter your password',
                obscureText: _obscurePassword,
                prefixIcon: Icons.lock_outlined,
                suffixIcon: IconButton(
                  icon: Icon(
                    _obscurePassword ? Icons.visibility : Icons.visibility_off,
                  ),
                  onPressed: () {
                    setState(() {
                      _obscurePassword = !_obscurePassword;
                    });
                  },
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Password is required';
                  }
                  if (value.length < 6) {
                    return 'Password must be at least 6 characters';
                  }
                  return null;
                },
                onChanged: (value) => setState(() {}), // Trigger rebuild for strength indicator
              ),
              
              // Password Strength Indicator
              if (_passwordController.text.isNotEmpty)
                _buildPasswordStrengthIndicator(authProvider),
              
              const SizedBox(height: 16),
              
              // Confirm Password Field
              CustomTextField(
                controller: _confirmPasswordController,
                label: 'Confirm Password',
                hint: 'Confirm your password',
                obscureText: _obscureConfirmPassword,
                prefixIcon: Icons.lock_outlined,
                suffixIcon: IconButton(
                  icon: Icon(
                    _obscureConfirmPassword ? Icons.visibility : Icons.visibility_off,
                  ),
                  onPressed: () {
                    setState(() {
                      _obscureConfirmPassword = !_obscureConfirmPassword;
                    });
                  },
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Please confirm your password';
                  }
                  if (value != _passwordController.text) {
                    return 'Passwords do not match';
                  }
                  return null;
                },
              ),
              
              const SizedBox(height: 16),
              
              // Terms and Conditions
              Row(
                children: [
                  Checkbox(
                    value: _acceptTerms,
                    onChanged: (value) {
                      setState(() {
                        _acceptTerms = value ?? false;
                      });
                    },
                  ),
                  Expanded(
                    child: GestureDetector(
                      onTap: () {
                        setState(() {
                          _acceptTerms = !_acceptTerms;
                        });
                      },
                      child: const Text(
                        'I agree to the Terms of Service and Privacy Policy',
                        style: TextStyle(fontSize: 14),
                      ),
                    ),
                  ),
                ],
              ),
              
              const SizedBox(height: 24),
              
              // Register Button
              CustomButton(
                text: 'Create Account',
                onPressed: _acceptTerms ? () => _handleRegister(authProvider) : null,
                isLoading: authProvider.isLoading,
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildPasswordStrengthIndicator(AuthProvider authProvider) {
    int strength = authProvider.getPasswordStrength(_passwordController.text);
    Color strengthColor;
    String strengthText;
    
    switch (strength) {
      case 0:
      case 1:
        strengthColor = Colors.red;
        strengthText = 'Weak';
        break;
      case 2:
      case 3:
        strengthColor = Colors.orange;
        strengthText = 'Medium';
        break;
      case 4:
      case 5:
        strengthColor = Colors.green;
        strengthText = 'Strong';
        break;
      default:
        strengthColor = Colors.grey;
        strengthText = 'Unknown';
    }
    
    return Padding(
      padding: const EdgeInsets.only(top: 8),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Row(
            children: [
              Expanded(
                child: LinearProgressIndicator(
                  value: strength / 5,
                  backgroundColor: Colors.grey.shade300,
                  valueColor: AlwaysStoppedAnimation<Color>(strengthColor),
                ),
              ),
              const SizedBox(width: 8),
              Text(
                strengthText,
                style: TextStyle(
                  color: strengthColor,
                  fontSize: 12,
                  fontWeight: FontWeight.w500,
                ),
              ),
            ],
          ),
        ],
      ),
    );
  }

  Widget _buildErrorMessage(String message) {
    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: Colors.red.shade50,
        borderRadius: BorderRadius.circular(8),
        border: Border.all(color: Colors.red.shade200),
      ),
      child: Row(
        children: [
          Icon(Icons.error_outline, color: Colors.red.shade600),
          const SizedBox(width: 8),
          Expanded(
            child: Text(
              message,
              style: TextStyle(
                color: Colors.red.shade600,
                fontSize: 14,
              ),
            ),
          ),
        ],
      ),
    );
  }

  Widget _buildSignInLink() {
    return Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        const Text(
          "Already have an account? ",
          style: TextStyle(color: Colors.white70),
        ),
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: const Text(
            'Sign In',
            style: TextStyle(
              color: Colors.white,
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
      ],
    );
  }

  Future<void> _handleRegister(AuthProvider authProvider) async {
    if (!_formKey.currentState!.validate()) return;

    if (!_acceptTerms) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Please accept the terms and conditions'),
          backgroundColor: Colors.red,
        ),
      );
      return;
    }

    // Clear any previous errors
    authProvider.clearError();

    bool success = await authProvider.signUp(
      email: _emailController.text.trim(),
      password: _passwordController.text,
      name: _nameController.text.trim(),
      phoneNumber: _phoneController.text.trim().isEmpty 
          ? null 
          : _phoneController.text.trim(),
    );

    if (success) {
      // Navigation is handled by AuthWrapper in main.dart
    } else {
      // Error is already set in provider, UI will show it
    }
  }
}


screens/home/home_screen.dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/providers/group_provider.dart';
import 'package:astra/providers/location_provider.dart';
import 'package:astra/models/group.dart';
import 'package:astra/screens/group/create_group_screen.dart';
import 'package:astra/screens/group/join_group_screen.dart';
import 'package:astra/screens/group/group_details_screen.dart';
import 'package:astra/screens/map/map_screen.dart';
import 'package:astra/screens/profile/profile_screen.dart';
import 'package:astra/widgets/common/custom_button.dart';
import 'package:astra/config/theme.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  int _currentIndex = 0;
  
  @override
  void initState() {
    super.initState();
    _initializeApp();
  }

  void _initializeApp() async {
    final authProvider = Provider.of<AuthProvider>(context, listen: false);
    final groupProvider = Provider.of<GroupProvider>(context, listen: false);
    final locationProvider = Provider.of<LocationProvider>(context, listen: false);
    
    // Initialize location services
    await locationProvider.initialize();
    
    // Load user's groups
    if (authProvider.currentUserId != null) {
      groupProvider.loadUserGroups(authProvider.currentUserId!);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: const [
          _HomeTab(),
          MapScreen(),
          _GroupsTab(),
          ProfileScreen(),
        ],
      ),
      bottomNavigationBar: _buildBottomNavigationBar(),
    );
  }

  Widget _buildBottomNavigationBar() {
    return Consumer<GroupProvider>(
      builder: (context, groupProvider, child) {
        return BottomNavigationBar(
          currentIndex: _currentIndex,
          onTap: (index) {
            setState(() {
              _currentIndex = index;
            });
          },
          type: BottomNavigationBarType.fixed,
          items: [
            const BottomNavigationBarItem(
              icon: Icon(Icons.home),
              label: 'Home',
            ),
            BottomNavigationBarItem(
              icon: Stack(
                children: [
                  const Icon(Icons.map),
                  if (groupProvider.hasCurrentGroup && groupProvider.isInActiveGroup)
                    Positioned(
                      right: 0,
                      top: 0,
                      child: Container(
                        width: 8,
                        height: 8,
                        decoration: const BoxDecoration(
                          color: Colors.green,
                          shape: BoxShape.circle,
                        ),
                      ),
                    ),
                ],
              ),
              label: 'Map',
            ),
            BottomNavigationBarItem(
              icon: Stack(
                children: [
                  const Icon(Icons.group),
                  if (groupProvider.userGroups.isNotEmpty)
                    Positioned(
                      right: 0,
                      top: 0,
                      child: Container(
                        padding: const EdgeInsets.all(2),
                        decoration: const BoxDecoration(
                          color: AppTheme.primaryColor,
                          shape: BoxShape.circle,
                        ),
                        constraints: const BoxConstraints(
                          minWidth: 16,
                          minHeight: 16,
                        ),
                        child: Text(
                          '${groupProvider.userGroups.length}',
                          style: const TextStyle(
                            color: Colors.white,
                            fontSize: 10,
                            fontWeight: FontWeight.bold,
                          ),
                          textAlign: TextAlign.center,
                        ),
                      ),
                    ),
                ],
              ),
              label: 'Groups',
            ),
            const BottomNavigationBarItem(
              icon: Icon(Icons.person),
              label: 'Profile',
            ),
          ],
        );
      },
    );
  }
}

class _HomeTab extends StatelessWidget {
  const _HomeTab();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Astra'),
        actions: [
          Consumer<AuthProvider>(
            builder: (context, authProvider, child) {
              return Padding(
                padding: const EdgeInsets.all(8.0),
                child: CircleAvatar(
                  backgroundColor: Colors.white,
                  child: Text(
                    authProvider.currentUser?.initials ?? 'U',
                    style: const TextStyle(
                      color: AppTheme.primaryColor,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                ),
              );
            },
          ),
        ],
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Welcome Section
            _buildWelcomeSection(),
            
            const SizedBox(height: 24),
            
            // Current Group Status
            _buildCurrentGroupStatus(),
            
            const SizedBox(height: 24),
            
            // Quick Actions
            _buildQuickActions(),
            
            const SizedBox(height: 24),
            
            // Recent Groups
            _buildRecentGroups(),
            
            const SizedBox(height: 24),
            
            // Location Status
            _buildLocationStatus(),
          ],
        ),
      ),
    );
  }

  Widget _buildWelcomeSection() {
    return Consumer<AuthProvider>(
      builder: (context, authProvider, child) {
        return Card(
          child: Padding(
            padding: const EdgeInsets.all(20.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  'Welcome back, ${authProvider.currentUser?.displayName ?? 'Rider'}!',
                  style: Theme.of(context).textTheme.headlineMedium,
                ),
                const SizedBox(height: 8),
                Text(
                  'Ready for your next group ride?',
                  style: Theme.of(context).textTheme.bodyLarge?.copyWith(
                    color: AppTheme.textSecondary,
                  ),
                ),
              ],
            ),
          ),
        );
      },
    );
  }

  Widget _buildCurrentGroupStatus() {
    return Consumer<GroupProvider>(
      builder: (context, groupProvider, child) {
        if (!groupProvider.hasCurrentGroup) {
          return _buildNoGroupCard();
        }

        return _buildActiveGroupCard(groupProvider.currentGroup!);
      },
    );
  }

  Widget _buildNoGroupCard() {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Icon(
                  Icons.group_off,
                  color: AppTheme.textSecondary,
                  size: 32,
                ),
                const SizedBox(width: 12),
                Expanded(
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        'No Active Group',
                        style: Theme.of(context).textTheme.headlineMedium,
                      ),
                      Text(
                        'Create or join a group to start riding together',
                        style: Theme.of(context).textTheme.bodyMedium,
                      ),
                    ],
                  ),
                ),
              ],
            ),
            const SizedBox(height: 16),
            Row(
              children: [
                Expanded(
                  child: CustomButton(
                    text: 'Create Group',
                    onPressed: () {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => const CreateGroupScreen(),
                        ),
                      );
                    },
                  ),
                ),
                const SizedBox(width: 12),
                Expanded(
                  child: CustomButton(
                    text: 'Join Group',
                    onPressed: () {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => const JoinGroupScreen(),
                        ),
                      );
                    },
                    variant: ButtonVariant.outlined,
                  ),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildActiveGroupCard(Group group) {
    return Card(
      color: AppTheme.primaryColor.withOpacity(0.1),
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Container(
                  padding: const EdgeInsets.all(8),
                  decoration: BoxDecoration(
                    color: _getStatusColor(group.status).withOpacity(0.2),
                    borderRadius: BorderRadius.circular(8),
                  ),
                  child: Icon(
                    _getStatusIcon(group.status),
                    color: _getStatusColor(group.status),
                    size: 24,
                  ),
                ),
                const SizedBox(width: 12),
                Expanded(
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        group.name,
                        style: Theme.of(context).textTheme.headlineMedium,
                      ),
                      Text(
                        _getStatusText(group.status),
                        style: Theme.of(context).textTheme.bodyMedium?.copyWith(
                          color: _getStatusColor(group.status),
                          fontWeight: FontWeight.w600,
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
            const SizedBox(height: 16),
            Row(
              children: [
                _buildGroupStat(
                  icon: Icons.people,
                  label: 'Members',
                  value: '${group.onlineMembers.length}/${group.memberCount}',
                ),
                const SizedBox(width: 20),
                _buildGroupStat(
                  icon: Icons.location_on,
                  label: 'Destination',
                  value: group.destination != null ? 'Set' : 'None',
                ),
                const SizedBox(width: 20),
                if (group.rideDuration != null)
                  _buildGroupStat(
                    icon: Icons.timer,
                    label: 'Duration',
                    value: _formatDuration(group.rideDuration!),
                  ),
              ],
            ),
            const SizedBox(height: 16),
            CustomButton(
              text: 'View Group',
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) => GroupDetailsScreen(group: group),
                  ),
                );
              },
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildGroupStat({
    required IconData icon,
    required String label,
    required String value,
  }) {
    return Column(
      children: [
        Icon(icon, size: 20, color: AppTheme.textSecondary),
        const SizedBox(height: 4),
        Text(
          value,
          style: const TextStyle(
            fontWeight: FontWeight.bold,
            fontSize: 14,
          ),
        ),
        Text(
          label,
          style: const TextStyle(
            fontSize: 12,
            color: AppTheme.textSecondary,
          ),
        ),
      ],
    );
  }

  Widget _buildQuickActions() {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          'Quick Actions',
          style: Theme.of(context).textTheme.headlineMedium,
        ),
        const SizedBox(height: 16),
        Row(
          children: [
            Expanded(
              child: _buildQuickActionCard(
                icon: Icons.add_circle,
                title: 'Create Group',
                subtitle: 'Start a new ride',
                color: AppTheme.primaryColor,
                onTap: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (context) => const CreateGroupScreen(),
                    ),
                  );
                },
              ),
            ),
            const SizedBox(width: 12),
            Expanded(
              child: _buildQuickActionCard(
                icon: Icons.group_add,
                title: 'Join Group',
                subtitle: 'Enter group code',
                color: AppTheme.accentColor,
                onTap: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (context) => const JoinGroupScreen(),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ],
    );
  }

  Widget _buildQuickActionCard({
    required IconData icon,
    required String title,
    required String subtitle,
    required Color color,
    required VoidCallback onTap,
  }) {
    return Card(
      child: InkWell(
        onTap: onTap,
        borderRadius: BorderRadius.circular(12),
        child: Padding(
          padding: const EdgeInsets.all(16.0),
          child: Column(
            children: [
              Container(
                padding: const EdgeInsets.all(12),
                decoration: BoxDecoration(
                  color: color.withOpacity(0.1),
                  borderRadius: BorderRadius.circular(12),
                ),
                child: Icon(
                  icon,
                  color: color,
                  size: 32,
                ),
              ),
              const SizedBox(height: 12),
              Text(
                title,
                style: const TextStyle(
                  fontWeight: FontWeight.bold,
                  fontSize: 16,
                ),
                textAlign: TextAlign.center,
              ),
              const SizedBox(height: 4),
              Text(
                subtitle,
                style: const TextStyle(
                  color: AppTheme.textSecondary,
                  fontSize: 12,
                ),
                textAlign: TextAlign.center,
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildRecentGroups() {
    return Consumer<GroupProvider>(
      builder: (context, groupProvider, child) {
        if (groupProvider.userGroups.isEmpty) {
          return const SizedBox.shrink();
        }

        return Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'Your Groups',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
            const SizedBox(height: 16),
            ...groupProvider.userGroups.take(3).map((group) => 
              _buildGroupListItem(group)),
            if (groupProvider.userGroups.length > 3)
              TextButton(
                onPressed: () {
                  // Switch to groups tab
                },
                child: const Text('View All Groups'),
              ),
          ],
        );
      },
    );
  }

  Widget _buildGroupListItem(Group group) {
    return Card(
      margin: const EdgeInsets.only(bottom: 8),
      child: ListTile(
        leading: Container(
          padding: const EdgeInsets.all(8),
          decoration: BoxDecoration(
            color: _getStatusColor(group.status).withOpacity(0.2),
            borderRadius: BorderRadius.circular(8),
          ),
          child: Icon(
            _getStatusIcon(group.status),
            color: _getStatusColor(group.status),
          ),
        ),
        title: Text(group.name),
        subtitle: Text(
          '${group.memberCount} members • ${_getStatusText(group.status)}',
        ),
        trailing: const Icon(Icons.arrow_forward_ios, size: 16),
        onTap: () {
          Navigator.push(
            context,
            MaterialPageRoute(
              builder: (context) => GroupDetailsScreen(group: group),
            ),
          );
        },
      ),
    );
  }

  Widget _buildLocationStatus() {
    return Consumer<LocationProvider>(
      builder: (context, locationProvider, child) {
        return Card(
          child: Padding(
            padding: const EdgeInsets.all(16.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Row(
                  children: [
                    Icon(
                      _getLocationIcon(locationProvider.status),
                      color: _getLocationColor(locationProvider.status),
                    ),
                    const SizedBox(width: 8),
                    Text(
                      'Location Status',
                      style: Theme.of(context).textTheme.titleMedium,
                    ),
                  ],
                ),
                const SizedBox(height: 8),
                Text(
                  locationProvider.getStatusMessage(),
                  style: Theme.of(context).textTheme.bodyMedium,
                ),
                if (locationProvider.hasLocation) ...[
                  const SizedBox(height: 8),
                  Text(
                    'Speed: ${locationProvider.currentSpeed} • Accuracy: ${locationProvider.accuracyStatus}',
                    style: Theme.of(context).textTheme.bodySmall?.copyWith(
                      color: AppTheme.textSecondary,
                    ),
                  ),
                ],
                if (locationProvider.status == LocationStatus.permissionDenied ||
                    locationProvider.status == LocationStatus.serviceDisabled) ...[
                  const SizedBox(height: 12),
                  CustomButton(
                    text: 'Enable Location',
                    onPressed: () => locationProvider.openLocationSettings(),
                    variant: ButtonVariant.outlined,
                  ),
                ],
              ],
            ),
          ),
        );
      },
    );
  }

  // Helper methods
  Color _getStatusColor(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return AppTheme.successColor;
      case GroupStatus.paused:
        return Colors.orange;
      case GroupStatus.completed:
        return Colors.blue;
      case GroupStatus.planning:
        return AppTheme.primaryColor;
      case GroupStatus.cancelled:
        return AppTheme.errorColor;
    }
  }

  IconData _getStatusIcon(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return Icons.directions_bike;
      case GroupStatus.paused:
        return Icons.pause;
      case GroupStatus.completed:
        return Icons.check_circle;
      case GroupStatus.planning:
        return Icons.schedule;
      case GroupStatus.cancelled:
        return Icons.cancel;
    }
  }

  String _getStatusText(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return 'Riding Now';
      case GroupStatus.paused:
        return 'On Break';
      case GroupStatus.completed:
        return 'Completed';
      case GroupStatus.planning:
        return 'Planning';
      case GroupStatus.cancelled:
        return 'Cancelled';
    }
  }

  IconData _getLocationIcon(LocationStatus status) {
    switch (status) {
      case LocationStatus.tracking:
        return Icons.my_location;
      case LocationStatus.stopped:
        return Icons.location_off;
      case LocationStatus.permissionDenied:
        return Icons.location_disabled;
      case LocationStatus.serviceDisabled:
        return Icons.location_disabled;
      case LocationStatus.error:
        return Icons.error;
      case LocationStatus.uninitialized:
        return Icons.location_searching;
    }
  }

  Color _getLocationColor(LocationStatus status) {
    switch (status) {
      case LocationStatus.tracking:
        return AppTheme.successColor;
      case LocationStatus.stopped:
        return Colors.orange;
      case LocationStatus.permissionDenied:
      case LocationStatus.serviceDisabled:
      case LocationStatus.error:
        return AppTheme.errorColor;
      case LocationStatus.uninitialized:
        return AppTheme.textSecondary;
    }
  }

  String _formatDuration(Duration duration) {
    int hours = duration.inHours;
    int minutes = duration.inMinutes.remainder(60);
    if (hours > 0) {
      return '${hours}h ${minutes}m';
    } else {
      return '${minutes}m';
    }
  }
}

class _GroupsTab extends StatelessWidget {
  const _GroupsTab();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Groups'),
        actions: [
          IconButton(
            icon: const Icon(Icons.add),
            onPressed: () {
              _showCreateJoinDialog(context);
            },
          ),
        ],
      ),
      body: Consumer<GroupProvider>(
        builder: (context, groupProvider, child) {
          if (groupProvider.userGroups.isEmpty) {
            return _buildEmptyState(context);
          }

          return _buildGroupsList(context, groupProvider.userGroups);
        },
      ),
    );
  }

  Widget _buildEmptyState(BuildContext context) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(32.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(
              Icons.group_off,
              size: 80,
              color: Colors.grey.shade400,
            ),
            const SizedBox(height: 24),
            Text(
              'No Groups Yet',
              style: Theme.of(context).textTheme.headlineMedium?.copyWith(
                color: Colors.grey.shade600,
              ),
            ),
            const SizedBox(height: 8),
            Text(
              'Create or join a group to start riding with others',
              style: Theme.of(context).textTheme.bodyMedium?.copyWith(
                color: Colors.grey.shade500,
              ),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 32),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                CustomButton(
                  text: 'Create Group',
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const CreateGroupScreen(),
                      ),
                    );
                  },
                ),
                const SizedBox(width: 16),
                CustomButton(
                  text: 'Join Group',
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const JoinGroupScreen(),
                      ),
                    );
                  },
                  variant: ButtonVariant.outlined,
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildGroupsList(BuildContext context, List<Group> groups) {
    return ListView.builder(
      padding: const EdgeInsets.all(16),
      itemCount: groups.length,
      itemBuilder: (context, index) {
        final group = groups[index];
        return Card(
          margin: const EdgeInsets.only(bottom: 12),
          child: ListTile(
            contentPadding: const EdgeInsets.all(16),
            leading: Container(
              padding: const EdgeInsets.all(12),
              decoration: BoxDecoration(
                color: _getStatusColor(group.status).withOpacity(0.2),
                borderRadius: BorderRadius.circular(12),
              ),
              child: Icon(
                _getStatusIcon(group.status),
                color: _getStatusColor(group.status),
              ),
            ),
            title: Text(
              group.name,
              style: const TextStyle(fontWeight: FontWeight.bold),
            ),
            subtitle: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                const SizedBox(height: 4),
                Text(group.description),
                const SizedBox(height: 8),
                Row(
                  children: [
                    Icon(Icons.people, size: 16, color: Colors.grey.shade600),
                    const SizedBox(width: 4),
                    Text('${group.memberCount} members'),
                    const SizedBox(width: 16),
                    Container(
                      padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 2),
                      decoration: BoxDecoration(
                        color: _getStatusColor(group.status).withOpacity(0.2),
                        borderRadius: BorderRadius.circular(12),
                      ),
                      child: Text(
                        _getStatusText(group.status),
                        style: TextStyle(
                          color: _getStatusColor(group.status),
                          fontSize: 12,
                          fontWeight: FontWeight.w600,
                        ),
                      ),
                    ),
                  ],
                ),
              ],
            ),
            trailing: const Icon(Icons.arrow_forward_ios, size: 16),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => GroupDetailsScreen(group: group),
                ),
              );
            },
          ),
        );
      },
    );
  }

  void _showCreateJoinDialog(BuildContext context) {
    showModalBottomSheet(
      context: context,
      builder: (context) => Container(
        padding: const EdgeInsets.all(24),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(
              'Group Options',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
            const SizedBox(height: 24),
            Row(
              children: [
                Expanded(
                  child: CustomButton(
                    text: 'Create Group',
                    onPressed: () {
                      Navigator.pop(context);
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => const CreateGroupScreen(),
                        ),
                      );
                    },
                  ),
                ),
                const SizedBox(width: 16),
                Expanded(
                  child: CustomButton(
                    text: 'Join Group',
                    onPressed: () {
                      Navigator.pop(context);
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => const JoinGroupScreen(),
                        ),
                      );
                    },
                    variant: ButtonVariant.outlined,
                  ),
                ),
              ],
            ),
            const SizedBox(height: 16),
          ],
        ),
      ),
    );
  }

  // Helper methods (same as in _HomeTab)
  Color _getStatusColor(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return AppTheme.successColor;
      case GroupStatus.paused:
        return Colors.orange;
      case GroupStatus.completed:
        return Colors.blue;
      case GroupStatus.planning:
        return AppTheme.primaryColor;
      case GroupStatus.cancelled:
        return AppTheme.errorColor;
    }
  }

  IconData _getStatusIcon(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return Icons.directions_bike;
      case GroupStatus.paused:
        return Icons.pause;
      case GroupStatus.completed:
        return Icons.check_circle;
      case GroupStatus.planning:
        return Icons.schedule;
      case GroupStatus.cancelled:
        return Icons.cancel;
    }
  }

  String _getStatusText(GroupStatus status) {
    switch (status) {
      case GroupStatus.active:
        return 'Active';
      case GroupStatus.paused:
        return 'Paused';
      case GroupStatus.completed:
        return 'Completed';
      case GroupStatus.planning:
        return 'Planning';
      case GroupStatus.cancelled:
        return 'Cancelled';
    }
  }
}


screens/map/map_screen.dart
import 'dart:async';
import 'package:flutter/material.dart';
import 'package:google_maps_flutter/google_maps_flutter.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/providers/group_provider.dart';
import 'package:astra/providers/location_provider.dart';
import 'package:astra/models/location.dart';
import 'package:astra/models/group.dart';
import 'package:astra/services/map_service.dart';
import 'package:astra/config/theme.dart';
import 'package:astra/widgets/map/rider_marker.dart';
import 'package:astra/widgets/common/custom_button.dart';

class MapScreen extends StatefulWidget {
  const MapScreen({super.key});

  @override
  State<MapScreen> createState() => _MapScreenState();
}

class _MapScreenState extends State<MapScreen> {
  final Completer<GoogleMapController> _controller = Completer();
  final MapService _mapService = MapService();
  
  Set<Marker> _markers = {};
  Set<Polyline> _polylines = {};
  Timer? _locationUpdateTimer;
  bool _isTrackingLocation = false;
  bool _showMembersList = false;

  @override
  void initState() {
    super.initState();
    _initializeMap();
  }

  @override
  void dispose() {
    _locationUpdateTimer?.cancel();
    super.dispose();
  }

  void _initializeMap() async {
    final locationProvider = Provider.of<LocationProvider>(context, listen: false);
    
    // Initialize location if not already done
    if (locationProvider.status == LocationStatus.uninitialized) {
      await locationProvider.initialize();
    }

    // Start location tracking if we have a group
    final groupProvider = Provider.of<GroupProvider>(context, listen: false);
    if (groupProvider.hasCurrentGroup) {
      _startLocationTracking();
    }
  }

  void _startLocationTracking() async {
    final authProvider = Provider.of<AuthProvider>(context, listen: false);
    final locationProvider = Provider.of<LocationProvider>(context, listen: false);
    final groupProvider = Provider.of<GroupProvider>(context, listen: false);

    if (authProvider.currentUserId == null || !groupProvider.hasCurrentGroup) {
      return;
    }

    // Start location tracking
    bool success = await locationProvider.startTracking(
      userId: authProvider.currentUserId!,
    );

    if (success) {
      setState(() {
        _isTrackingLocation = true;
      });

      // Update group with location every 5 seconds
      _locationUpdateTimer = Timer.periodic(
        const Duration(seconds: 5),
        (timer) async {
          if (locationProvider.currentLocation != null) {
            await groupProvider.updateMemberLocation(
              authProvider.currentUserId!,
              locationProvider.currentLocation!,
            );
          }
        },
      );
    }
  }

  void _stopLocationTracking() async {
    final locationProvider = Provider.of<LocationProvider>(context, listen: false);
    
    await locationProvider.stopTracking();
    _locationUpdateTimer?.cancel();
    
    setState(() {
      _isTrackingLocation = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Group Map'),
        actions: [
          Consumer<GroupProvider>(
            builder: (context, groupProvider, child) {
              if (!groupProvider.hasCurrentGroup) return const SizedBox.shrink();
              
              return Row(
                children: [
                  // Members list toggle
                  IconButton(
                    icon: Icon(_showMembersList ? Icons.list : Icons.people),
                    onPressed: () {
                      setState(() {
                        _showMembersList = !_showMembersList;
                      });
                    },
                  ),
                  // Center on group
                  IconButton(
                    icon: const Icon(Icons.center_focus_strong),
                    onPressed: _centerOnGroup,
                  ),
                ],
              );
            },
          ),
        ],
      ),
      body: Consumer3<GroupProvider, LocationProvider, AuthProvider>(
        builder: (context, groupProvider, locationProvider, authProvider, child) {
          return Stack(
            children: [
              // Map
              _buildMap(groupProvider, locationProvider, authProvider),
              
              // No group overlay
              if (!groupProvider.hasCurrentGroup)
                _buildNoGroupOverlay(),
              
              // Location status banner
              if (locationProvider.status != LocationStatus.tracking)
                _buildLocationStatusBanner(locationProvider),
              
              // Members list overlay
              if (_showMembersList && groupProvider.hasCurrentGroup)
                _buildMembersListOverlay(groupProvider.currentGroup!),
              
              // Emergency alert banner
              if (groupProvider.hasCurrentGroup)
                _buildEmergencyAlertBanner(groupProvider.currentGroup!),
              
              // Bottom controls
              _buildBottomControls(groupProvider, locationProvider, authProvider),
            ],
          );
        },
      ),
    );
  }

  Widget _buildMap(GroupProvider groupProvider, LocationProvider locationProvider, AuthProvider authProvider) {
    return GoogleMap(
      mapType: MapType.normal,
      initialCameraPosition: const CameraPosition(
        target: LatLng(37.7749, -122.4194), // Default to San Francisco
        zoom: 14.0,
      ),
      onMapCreated: (GoogleMapController controller) {
        _controller.complete(controller);
        _updateMapWithLocations();
      },
      markers: _markers,
      polylines: _polylines,
      myLocationEnabled: true,
      myLocationButtonEnabled: false, // We'll use custom button
      compassEnabled: true,
      trafficEnabled: false,
      buildingsEnabled: true,
      indoorViewEnabled: false,
      mapToolbarEnabled: false,
      zoomControlsEnabled: false,
      rotateGesturesEnabled: true,
      scrollGesturesEnabled: true,
      tiltGesturesEnabled: true,
      zoomGesturesEnabled: true,
      minMaxZoomPreference: const MinMaxZoomPreference(8.0, 20.0),
    );
  }

  Widget _buildNoGroupOverlay() {
    return Container(
      color: Colors.black54,
      child: Center(
        child: Card(
          margin: const EdgeInsets.all(32),
          child: Padding(
            padding: const EdgeInsets.all(24),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                Icon(
                  Icons.group_off,
                  size: 64,
                  color: Colors.grey.shade600,
                ),
                const SizedBox(height: 16),
                Text(
                  'No Active Group',
                  style: Theme.of(context).textTheme.headlineMedium,
                  textAlign: TextAlign.center,
                ),
                const SizedBox(height: 8),
                Text(
                  'Join or create a group to see member locations on the map',
                  style: Theme.of(context).textTheme.bodyMedium,
                  textAlign: TextAlign.center,
                ),
                const SizedBox(height: 24),
                CustomButton(
                  text: 'Go to Groups',
                  onPressed: () {
                    // This would switch to groups tab in parent
                    Navigator.pop(context);
                  },
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }

  Widget _buildLocationStatusBanner(LocationProvider locationProvider) {
    if (locationProvider.status == LocationStatus.tracking) {
      return const SizedBox.shrink();
    }

    Color backgroundColor;
    IconData icon;
    String message;

    switch (locationProvider.status) {
      case LocationStatus.permissionDenied:
        backgroundColor = Colors.red;
        icon = Icons.location_disabled;
        message = 'Location permission required';
        break;
      case LocationStatus.serviceDisabled:
        backgroundColor = Colors.orange;
        icon = Icons.location_off;
        message = 'Location services disabled';
        break;
      case LocationStatus.error:
        backgroundColor = Colors.red;
        icon = Icons.error;
        message = locationProvider.errorMessage ?? 'Location error';
        break;
      default:
        backgroundColor = Colors.blue;
        icon = Icons.location_searching;
        message = 'Getting location...';
    }

    return Positioned(
      top: 0,
      left: 0,
      right: 0,
      child: Container(
        color: backgroundColor,
        padding: const EdgeInsets.all(12),
        child: Row(
          children: [
            Icon(icon, color: Colors.white),
            const SizedBox(width: 8),
            Expanded(
              child: Text(
                message,
                style: const TextStyle(color: Colors.white),
              ),
            ),
            if (locationProvider.status == LocationStatus.permissionDenied ||
                locationProvider.status == LocationStatus.serviceDisabled)
              TextButton(
                onPressed: () => locationProvider.openLocationSettings(),
                child: const Text(
                  'Settings',
                  style: TextStyle(color: Colors.white),
                ),
              ),
          ],
        ),
      ),
    );
  }

  Widget _buildMembersListOverlay(Group group) {
    return Positioned(
      top: 80,
      right: 16,
      child: Container(
        width: 300,
        constraints: const BoxConstraints(maxHeight: 400),
        child: Card(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Padding(
                padding: const EdgeInsets.all(16),
                child: Row(
                  children: [
                    const Icon(Icons.people),
                    const SizedBox(width: 8),
                    Expanded(
                      child: Text(
                        'Group Members',
                        style: Theme.of(context).textTheme.titleMedium,
                      ),
                    ),
                    IconButton(
                      icon: const Icon(Icons.close),
                      onPressed: () {
                        setState(() {
                          _showMembersList = false;
                        });
                      },
                    ),
                  ],
                ),
              ),
              const Divider(height: 1),
              Flexible(
                child: ListView.builder(
                  shrinkWrap: true,
                  itemCount: group.members.length,
                  itemBuilder: (context, index) {
                    final member = group.members[index];
                    return _buildMemberListItem(member);
                  },
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildMemberListItem(GroupMember member) {
    return ListTile(
      leading: CircleAvatar(
        backgroundColor: member.isOnline ? AppTheme.successColor : Colors.grey,
        child: Icon(
          member.role == MemberRole.leader ? Icons.star : Icons.person,
          color: Colors.white,
          size: 16,
        ),
      ),
      title: Text(
        member.name,
        style: TextStyle(
          fontWeight: member.role == MemberRole.leader 
              ? FontWeight.bold 
              : FontWeight.normal,
        ),
      ),
      subtitle: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text(
            member.role == MemberRole.leader ? 'Leader' : 'Member',
            style: const TextStyle(fontSize: 12),
          ),
          if (member.currentLocation != null)
            Text(
              'Speed: ${member.currentLocation!.speedKmh}',
              style: const TextStyle(fontSize: 10),
            ),
        ],
      ),
      trailing: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Container(
            width: 8,
            height: 8,
            decoration: BoxDecoration(
              color: member.isOnline ? AppTheme.successColor : Colors.grey,
              shape: BoxShape.circle,
            ),
          ),
          const SizedBox(height: 4),
          if (member.currentLocation?.isEmergency == true)
            const Icon(
              Icons.warning,
              color: AppTheme.emergencyColor,
              size: 16,
            ),
        ],
      ),
      onTap: () {
        if (member.currentLocation != null) {
          _centerOnLocation(LatLng(
            member.currentLocation!.latitude,
            member.currentLocation!.longitude,
          ));
        }
      },
    );
  }

  Widget _buildEmergencyAlertBanner(Group group) {
    final emergencyMembers = group.members
        .where((member) => member.currentLocation?.isEmergency == true)
        .toList();

    if (emergencyMembers.isEmpty) {
      return const SizedBox.shrink();
    }

    return Positioned(
      top: 80,
      left: 16,
      right: 16,
      child: Card(
        color: AppTheme.emergencyColor,
        child: Padding(
          padding: const EdgeInsets.all(16),
          child: Column(
            children: [
              Row(
                children: [
                  const Icon(
                    Icons.warning,
                    color: Colors.white,
                  ),
                  const SizedBox(width: 8),
                  Expanded(
                    child: Text(
                      'EMERGENCY ALERT',
                      style: Theme.of(context).textTheme.titleMedium?.copyWith(
                        color: Colors.white,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                  ),
                ],
              ),
              const SizedBox(height: 8),
              ...emergencyMembers.map((member) => Text(
                '${member.name}: ${member.currentLocation!.emergencyType?.name ?? 'Emergency'}',
                style: const TextStyle(color: Colors.white),
              )),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildBottomControls(GroupProvider groupProvider, LocationProvider locationProvider, AuthProvider authProvider) {
    return Positioned(
      bottom: 16,
      left: 16,
      right: 16,
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          // My location button
          FloatingActionButton(
            heroTag: "my_location",
            mini: true,
            backgroundColor: Colors.white,
            onPressed: _centerOnMyLocation,
            child: const Icon(Icons.my_location, color: AppTheme.primaryColor),
          ),
          
          // Location tracking toggle
          if (groupProvider.hasCurrentGroup)
            FloatingActionButton(
              heroTag: "location_tracking",
              mini: true,
              backgroundColor: _isTrackingLocation ? AppTheme.successColor : Colors.grey,
              onPressed: _isTrackingLocation ? _stopLocationTracking : _startLocationTracking,
              child: Icon(
                _isTrackingLocation ? Icons.stop : Icons.play_arrow,
                color: Colors.white,
              ),
            ),
          
          // Emergency button
          if (groupProvider.hasCurrentGroup)
            FloatingActionButton(
              heroTag: "emergency",
              mini: true,
              backgroundColor: AppTheme.emergencyColor,
              onPressed: () => _showEmergencyDialog(groupProvider, locationProvider, authProvider),
              child: const Icon(Icons.emergency, color: Colors.white),
            ),
          
          // Destination button
          if (groupProvider.hasCurrentGroup && groupProvider.isGroupLeader)
            FloatingActionButton(
              heroTag: "destination",
              mini: true,
              backgroundColor: AppTheme.primaryColor,
              onPressed: () => _showDestinationDialog(groupProvider, authProvider),
              child: const Icon(Icons.flag, color: Colors.white),
            ),
        ],
      ),
    );
  }

  void _updateMapWithLocations() async {
    final groupProvider = Provider.of<GroupProvider>(context, listen: false);
    final authProvider = Provider.of<AuthProvider>(context, listen: false);
    
    if (!groupProvider.hasCurrentGroup) {
      setState(() {
        _markers = {};
        _polylines = {};
      });
      return;
    }

    final group = groupProvider.currentGroup!;
    final currentUserId = authProvider.currentUserId ?? '';
    
    // Get member locations
    List<RiderLocation> memberLocations = group.members
        .where((member) => member.currentLocation != null)
        .map((member) => member.currentLocation!)
        .toList();

    // Create rider markers
    Set<Marker> riderMarkers = _mapService.createRiderMarkers(memberLocations, currentUserId);
    
    // Create stop markers
    Set<Marker> stopMarkers = _mapService.createStopMarkers(group.approvedStops);
    
    // Add destination marker
    Set<Marker> destinationMarkers = {};
    if (group.destination != null) {
      destinationMarkers.add(
        Marker(
          markerId: const MarkerId('destination'),
          position: LatLng(group.destination!.latitude, group.destination!.longitude),
          icon: BitmapDescriptor.defaultMarkerWithHue(BitmapDescriptor.hueBlue),
          infoWindow: InfoWindow(
            title: 'Destination',
            snippet: group.destination!.address ?? 'Group destination',
          ),
        ),
      );
    }

    // Create route polylines if we have destination
    Set<Polyline> routePolylines = {};
    if (group.destination != null && memberLocations.isNotEmpty) {
      try {
        List<LatLng> routePoints = await _mapService.getDirections(
          LatLng(memberLocations.first.latitude, memberLocations.first.longitude),
          LatLng(group.destination!.latitude, group.destination!.longitude),
        );
        routePolylines = _mapService.createRoutePolylines(routePoints);
      } catch (e) {
        print('Failed to get route: $e');
      }
    }

    setState(() {
      _markers = {...riderMarkers, ...stopMarkers, ...destinationMarkers};
      _polylines = routePolylines;
    });
  }

  void _centerOnGroup() async {
    final groupProvider = Provider.of<GroupProvider>(context, listen: false);
    
    if (!groupProvider.hasCurrentGroup) return;
    
    List<LatLng> positions = groupProvider.currentGroup!.members
        .where((member) => member.currentLocation != null)
        .map((member) => LatLng(
          member.currentLocation!.latitude,
          member.currentLocation!.longitude,
        ))
        .toList();

    if (positions.isEmpty) return;

    CameraPosition cameraPosition = _mapService.calculateOptimalCameraPosition(positions);
    
    final GoogleMapController controller = await _controller.future;
    controller.animateCamera(CameraUpdate.newCameraPosition(cameraPosition));
  }

  void _centerOnMyLocation() async {
    final locationProvider = Provider.of<LocationProvider>(context, listen: false);
    
    if (locationProvider.currentLocation == null) return;
    
    final GoogleMapController controller = await _controller.future;
    controller.animateCamera(
      CameraUpdate.newLatLng(
        LatLng(
          locationProvider.currentLocation!.latitude,
          locationProvider.currentLocation!.longitude,
        ),
      ),
    );
  }

  void _centerOnLocation(LatLng location) async {
    final GoogleMapController controller = await _controller.future;
    controller.animateCamera(
      CameraUpdate.newLatLng(location),
    );
  }

  void _showEmergencyDialog(GroupProvider groupProvider, LocationProvider locationProvider, AuthProvider authProvider) {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Emergency Alert'),
        content: const Text('Select emergency type:'),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: const Text('Cancel'),
          ),
          ...EmergencyType.values.map((type) => TextButton(
            onPressed: () async {
              Navigator.pop(context);
              if (locationProvider.currentLocation != null && authProvider.currentUserId != null) {
                await groupProvider.sendEmergencyAlert(
                  authProvider.currentUserId!,
                  locationProvider.currentLocation!,
                  type,
                );
                
                if (mounted) {
                  ScaffoldMessenger.of(context).showSnackBar(
                    SnackBar(
                      content: Text('Emergency alert sent: ${type.name}'),
                      backgroundColor: AppTheme.emergencyColor,
                    ),
                  );
                }
              }
            },
            child: Text(type.name.toUpperCase()),
          )),
        ],
      ),
    );
  }

  void _showDestinationDialog(GroupProvider groupProvider, AuthProvider authProvider) {
    final TextEditingController addressController = TextEditingController();
    
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Set Destination'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(
              controller: addressController,
              decoration: const InputDecoration(
                labelText: 'Destination Address',
                hintText: 'Enter address or place name',
              ),
            ),
          ],
        ),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: const Text('Cancel'),
          ),
          ElevatedButton(
            onPressed: () async {
              Navigator.pop(context);
              
              if (addressController.text.trim().isNotEmpty) {
                // Convert address to coordinates
                LatLng? coordinates = await _mapService.getCoordinatesFromAddress(
                  addressController.text.trim(),
                );
                
                if (coordinates != null && authProvider.currentUserId != null) {
                  RiderLocation destination = RiderLocation(
                    id: DateTime.now().millisecondsSinceEpoch.toString(),
                    latitude: coordinates.latitude,
                    longitude: coordinates.longitude,
                    timestamp: DateTime.now(),
                    type: LocationType.destination,
                    address: addressController.text.trim(),
                    description: 'Group destination',
                  );
                  
                  bool success = await groupProvider.updateDestination(
                    destination,
                    authProvider.currentUserId!,
                  );
                  
                  if (mounted) {
                    ScaffoldMessenger.of(context).showSnackBar(
                      SnackBar(
                        content: Text(success 
                            ? 'Destination set successfully' 
                            : 'Failed to set destination'),
                        backgroundColor: success ? AppTheme.successColor : AppTheme.errorColor,
                      ),
                    );
                  }
                  
                  if (success) {
                    _updateMapWithLocations();
                  }
                } else {
                  if (mounted) {
                    ScaffoldMessenger.of(context).showSnackBar(
                      const SnackBar(
                        content: Text('Could not find location'),
                        backgroundColor: AppTheme.errorColor,
                      ),
                    );
                  }
                }
              }
            },
            child: const Text('Set'),
          ),
        ],
      ),
    );
  }
}

screens/group/create_group_screen.dart

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/providers/group_provider.dart';
import 'package:astra/widgets/common/custom_button.dart';
import 'package:astra/widgets/common/custom_text_field.dart';
import 'package:astra/widgets/common/loading_widget.dart';
import 'package:astra/config/theme.dart';
import 'package:astra/utils/constants.dart';

class CreateGroupScreen extends StatefulWidget {
  const CreateGroupScreen({super.key});

  @override
  State<CreateGroupScreen> createState() => _CreateGroupScreenState();
}

class _CreateGroupScreenState extends State<CreateGroupScreen> {
  final _formKey = GlobalKey<FormState>();
  final _nameController = TextEditingController();
  final _descriptionController = TextEditingController();
  
  @override
  void dispose() {
    _nameController.dispose();
    _descriptionController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Create Group'),
        leading: IconButton(
          icon: const Icon(Icons.close),
          onPressed: () => Navigator.pop(context),
        ),
      ),
      body: Consumer2<GroupProvider, AuthProvider>(
        builder: (context, groupProvider, authProvider, child) {
          return Stack(
            children: [
              SingleChildScrollView(
                padding: const EdgeInsets.all(24.0),
                child: Form(
                  key: _formKey,
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.stretch,
                    children: [
                      // Header
                      _buildHeader(),
                      
                      const SizedBox(height: 32),
                      
                      // Group Details Form
                      _buildGroupForm(),
                      
                      const SizedBox(height: 24),
                      
                      // Group Rules Info
                      _buildGroupRules(),
                      
                      const SizedBox(height: 32),
                      
                      // Error Message
                      if (groupProvider.errorMessage != null)
                        _buildErrorMessage(groupProvider.errorMessage!),
                      
                      const SizedBox(height: 24),
                      
                      // Create Button
                      CustomButton(
                        text: 'Create Group',
                        onPressed: () => _handleCreateGroup(groupProvider, authProvider),
                        isLoading: groupProvider.isLoading,
                      ),
                      
                      const SizedBox(height: 16),
                      
                      // Cancel Button
                      CustomButton(
                        text: 'Cancel',
                        onPressed: () => Navigator.pop(context),
                        variant: ButtonVariant.outlined,
                      ),
                    ],
                  ),
                ),
              ),
              
              // Loading Overlay
              if (groupProvider.isLoading)
                const LoadingWidget(message: 'Creating group...'),
            ],
          );
        },
      ),
    );
  }

  Widget _buildHeader() {
    return Column(
      children: [
        Container(
          width: 80,
          height: 80,
          decoration: BoxDecoration(
            color: AppTheme.primaryColor.withOpacity(0.1),
            borderRadius: BorderRadius.circular(20),
          ),
          child: const Icon(
            Icons.group_add,
            size: 40,
            color: AppTheme.primaryColor,
          ),
        ),
        const SizedBox(height: 16),
        Text(
          'Create New Group',
          style: Theme.of(context).textTheme.headineLarge,
          textAlign: TextAlign.center,
        ),
        const SizedBox(height: 8),
        Text(
          'Set up a new riding group and invite your friends',
          style: Theme.of(context).textTheme.bodyMedium?.copyWith(
            color: AppTheme.textSecondary,
          ),
          textAlign: TextAlign.center,
        ),
      ],
    );
  }

  Widget _buildGroupForm() {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'Group Details',
              style: Theme.of(context).textTheme.titleLarge,
            ),
            const SizedBox(height: 16),
            
            // Group Name
            CustomTextField(
              controller: _nameController,
              label: 'Group Name',
              hint: 'Enter group name (e.g., Weekend Riders)',
              prefixIcon: Icons.group,
              validator: (value) {
                if (value == null || value.trim().isEmpty) {
                  return 'Group name is required';
                }
                if (value.trim().length < 3) {
                  return 'Group name must be at least 3 characters';
                }
                if (value.trim().length > 30) {
                  return 'Group name must be less than 30 characters';
                }
                return null;
              },
            ),
            
            const SizedBox(height: 16),
            
            // Group Description
            CustomTextField(
              controller: _descriptionController,
              label: 'Description (Optional)',
              hint: 'Describe your group or riding style',
              prefixIcon: Icons.description,
              maxLines: 3,
              validator: (value) {
                if (value != null && value.length > 200) {
                  return 'Description must be less than 200 characters';
                }
                return null;
              },
            ),
            
            const SizedBox(height: 16),
            
            // Character counter for description
            Align(
              alignment: Alignment.centerRight,
              child: Text(
                '${_descriptionController.text.length}/200',
                style: Theme.of(context).textTheme.bodySmall?.copyWith(
                  color: AppTheme.textSecondary,
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildGroupRules() {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                const Icon(
                  Icons.info_outline,
                  color: AppTheme.primaryColor,
                ),
                const SizedBox(width: 8),
                Text(
                  'Group Rules',
                  style: Theme.of(context).textTheme.titleMedium,
                ),
              ],
            ),
            const SizedBox(height: 12),
            _buildRuleItem('As group leader, you can set destinations and approve stops'),
            _buildRuleItem('Members can suggest stops and send emergency alerts'),
            _buildRuleItem('Maximum ${AppConstants.maxGroupMembers} members per group'),
            _buildRuleItem('Group codes are auto-generated for easy sharing'),
            _buildRuleItem('All members can see real-time locations during rides'),
          ],
        ),
      ),
    );
  }

  Widget _buildRuleItem(String text) {
    return Padding(
      padding: const EdgeInsets.only(bottom: 8.0),
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          const Icon(
            Icons.check_circle_outline,
            size: 16,
            color: AppTheme.successColor,
          ),
          const SizedBox(width: 8),
          Expanded(
            child: Text(
              text,
              style: Theme.of(context).textTheme.bodySmall,
            ),
          ),
        ],
      ),
    );
  }

  Widget _buildErrorMessage(String message) {
    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: Colors.red.shade50,
        borderRadius: BorderRadius.circular(8),
        border: Border.all(color: Colors.red.shade200),
      ),
      child: Row(
        children: [
          Icon(Icons.error_outline, color: Colors.red.shade600),
          const SizedBox(width: 8),
          Expanded(
            child: Text(
              message,
              style: TextStyle(
                color: Colors.red.shade600,
                fontSize: 14,
              ),
            ),
          ),
        ],
      ),
    );
  }

  Future<void> _handleCreateGroup(GroupProvider groupProvider, AuthProvider authProvider) async {
    if (!_formKey.currentState!.validate()) return;

    if (authProvider.currentUser == null) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('User not authenticated'),
          backgroundColor: AppTheme.errorColor,
        ),
      );
      return;
    }

    // Clear any previous errors
    groupProvider.clearError();

    bool success = await groupProvider.createGroup(
      name: _nameController.text.trim(),
      description: _descriptionController.text.trim(),
      leader: authProvider.currentUser!,
    );

    if (success && mounted) {
      // Show success message
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text(AppStrings.groupCreated),
          backgroundColor: AppTheme.successColor,
        ),
      );
      
      // Navigate back to home
      Navigator.pop(context);
    }
  }
}

screens/group/join_group_screen.dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:provider/provider.dart';
import 'package:astra/providers/auth_provider.dart';
import 'package:astra/providers/group_provider.dart';
import 'package:astra/widgets/common/custom_button.dart';
import 'package:astra/widgets/common/custom_text_field.dart';
import 'package:astra/widgets/common/loading_widget.dart';
import 'package:astra/config/theme.dart';
import 'package:astra/utils/constants.dart';

class JoinGroupScreen extends StatefulWidget {
  const JoinGroupScreen({super.key});

  @override
  State<JoinGroupScreen> createState() => _JoinGroupScreenState();
}

class _JoinGroupScreenState extends State<JoinGroupScreen> {
  final _formKey = GlobalKey<FormState>();
  final _codeController = TextEditingController();
  
  @override
  void dispose() {
    _codeController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Join Group'),
        leading: IconButton(
          icon: const Icon(Icons.close),
          onPressed: () => Navigator.pop(context),
        ),
      ),
      body: Consumer2<GroupProvider, AuthProvider>(
        builder: (context, groupProvider, authProvider, child) {
          return Stack(
            children: [
              SingleChildScrollView(
                padding: const EdgeInsets.all(24.0),
                child: Form(
                  key: _formKey,
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.stretch,
                    children: [
                      // Header
                      _buildHeader(),
                      
                      const SizedBox(height: 32),
                      
                      // Join Code Form
                      _buildJoinCodeForm(),
                      
                      const SizedBox(height: 24),
                      
                      // How to get code info
                      _buildHowToGetCode(),
                      
                      const SizedBox(height: 32),
                      
                      // Error Message
                      if (groupProvider.errorMessage != null)
                        _buildErrorMessage(groupProvider.errorMessage!),
                      
                      const SizedBox(height: 24),
                      
                      // Join Button
                      CustomButton(
                        text: 'Join Group',
                        onPressed: () => _handleJoinGroup(groupProvider, authProvider),
                        isLoading: groupProvider.isLoading,
                      ),
                      
                      const SizedBox(height: 16),
                      
                      // Cancel Button
                      CustomButton(
                        text: 'Cancel',
                        onPressed: () => Navigator.pop(context),
                        variant: ButtonVariant.outlined,
                      ),
                    ],
                  ),
                ),
              ),
              
              // Loading Overlay
              if (groupProvider.isLoading)
                const LoadingWidget(message: 'Joining group...'),
            ],
          );
        },
      ),
    );
  }

  Widget _buildHeader() {
    return Column(
      children: [
        Container(
          width: 80,
          height: 80,
          decoration: BoxDecoration(
            color: AppTheme.accentColor.withOpacity(0.1),
            borderRadius: BorderRadius.circular(20),
          ),
          child: const Icon(
            Icons.group_add,
            size: 40,
            color: AppTheme.accentColor,
          ),
        ),
        const SizedBox(height: 16),
        Text(
          'Join Riding Group',
          style: Theme.of(context).textTheme.headlineLarge,
          textAlign: TextAlign.center,
        ),
        const SizedBox(height: 8),
        Text(
          'Enter the group code to join an existing riding group',
          style: Theme.of(context).textTheme.bodyMedium?.copyWith(
            color: AppTheme.textSecondary,
          ),
          textAlign: TextAlign.center,
        ),
      ],
    );
  }

  Widget _buildJoinCodeForm() {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'Group Code',
              style: Theme.of(context).textTheme.titleLarge,
            ),
            const SizedBox(height: 16),
            
            // Join Code Input
            CustomTextField(
              controller: _codeController,
              label: 'Enter Group Code',
              hint: 'ABC123',
              prefixIcon: Icons.vpn_key,
              textCapitalization: TextCapitalization.characters,
              inputFormatters: [
                FilteringTextInputFormatter.allow(RegExp(r'[A-Z0-9]')),
                LengthLimitingTextInputFormatter(AppConstants.groupCodeLength),
              ],
              validator: (value) {
                if (value == null || value.trim().isEmpty) {
                  return 'Group code is required';
                }
                if (value.trim().length != AppConstants.groupCodeLength) {
                  return 'Group code must be ${AppConstants.groupCodeLength} characters';
                }
                return null;
              },
              suffixIcon: Row(
                mainAxisSize: MainAxisSize.min,
                children: [
                  // Paste button
                  IconButton(
                    icon: const Icon(Icons.paste),
                    onPressed: _pasteFromClipboard,
                    tooltip: 'Paste from clipboard',
                  ),
                  // Clear button
                  if (_codeController.text.isNotEmpty)
                    IconButton(
                      icon: const Icon(Icons.clear),
                      onPressed: () {
                        setState(() {
                          _codeController.clear();
                        });
                      },
                      tooltip: 'Clear',
                    ),
                ],
              ),
              onChanged: (value) {
                setState(() {}); // Rebuild to show/hide clear button
              },
            ),
            
            const SizedBox(height: 8),
            
            // Code format hint
            Text(
              'Format: ${AppConstants.groupCodeLength} characters (letters and numbers)',
              style: Theme.of(context).textTheme.bodySmall?.copyWith(
                color: AppTheme.textSecondary,
              ),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildHowToGetCode() {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                const Icon(
                  Icons.help_outline,
                  color: AppTheme.primaryColor,
                ),
                const SizedBox(width: 8),
                Text(
                  'How to get a group code?',
                  style: Theme.of(context).textTheme.titleMedium,
                ),
              ],
            ),
            const SizedBox(height: 12),
            _buildHelpItem(
              icon: Icons.person_add,
              title: 'Ask the group leader',
              description: 'The group leader can share the code with you',
            ),
            const SizedBox(height: 12),
            _buildHelpItem(
              icon: Icons.share,
              title: 'Check group invitations',
              description: 'Look for shared codes in messages or social media',
            ),
            const SizedBox(height: 12),
            _buildHelpItem(
              icon: Icons.qr_code,
              title: 'Scan QR code',
              description: 'Some groups may share QR codes (future feature)',
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildHelpItem({
    required IconData icon,
    required String title,
    required String description,
  }) {
    return Row(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          padding: const EdgeInsets.all(8),
          decoration: BoxDecoration(
            color: AppTheme.primaryColor.withOpacity(0.1),
            borderRadius: BorderRadius.circular(8),
          ),
          child: Icon(
            icon,
            size: 20,
            color: AppTheme.primaryColor,
          ),
        ),
        const SizedBox(width: 12),
        Expanded(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text(
                title,
                style: Theme.of(context).textTheme.titleSmall,
              ),
              const SizedBox(height: 4),
              Text(
                description,
                style: Theme.of(context).textTheme.bodySmall?.copyWith(
                  color: AppTheme.textSecondary,
                ),
              ),
            ],
          ),
        ),
      ],
    );
  }

  Widget _buildErrorMessage(String message) {
    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: Colors.red.shade50,
        borderRadius: BorderRadius.circular(8),
        border: Border.all(color: Colors.red.shade200),
      ),
      child: Row(
        children: [
          Icon(Icons.error_outline, color: Colors.red.shade600),
          const SizedBox(width: 8),
          Expanded(
            child: Text(
              message,
              style: TextStyle(
                color: Colors.red.shade600,
                fontSize: 14,
              ),
            ),
          ),
        ],
      ),
    );
  }

  Future<void> _pasteFromClipboard() async {
    try {
      ClipboardData? data = await Clipboard.getData(Clipboard.kTextPlain);
      if (data != null && data.text != null) {
        String pastedText = data.text!.toUpperCase().replaceAll(RegExp(r'[^A-Z0-9]'), '');
        if (pastedText.length <= AppConstants.groupCodeLength) {
          setState(() {
            _codeController.text = pastedText;
          });
        }
      }
    } catch (e) {
      // Handle clipboard access error
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Could not access clipboard'),
          backgroundColor: AppTheme.errorColor,
        ),
      );
    }
  }

  Future<void> _handleJoinGroup(GroupProvider groupProvider, AuthProvider authProvider) async {
    if (!_formKey.currentState!.validate()) return;

    if (authProvider.currentUser == null) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('User not authenticated'),
          backgroundColor: AppTheme.errorColor,
        ),
      );
      return;
    }

    // Clear any previous errors
    groupProvider.clearError();

    bool success = await groupProvider.joinGroup(
      _codeController.text.trim().toUpperCase(),
      authProvider.currentUser!,
    );

    if (success && mounted) {
      // Show success message
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text(AppStrings.groupJoined),
          backgroundColor: AppTheme.successColor,
        ),
      );
      
      // Navigate back to home
      Navigator.pop(context);
    }
  }
}













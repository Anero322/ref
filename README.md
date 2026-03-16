classDiagram
    %% ===== Модели данных =====
    class User {
        - id: number
        - login: string
        - name: string
        - role: string
        - email: string
        - phone: string
        - createdAt: string
        +hasRole(role) boolean
        +validate() string[]
    }
    
    class Vehicle {
        - id: number
        - brand: string
        - licensePlate: string
        - maxWeight: number
        - maxVolume: number
        - status: string
        - type: string
        +isAvailable() boolean
        +validate() string[]
    }
    
    class Product {
        - id: number
        - name: string
        - weight: number
        - length: number
        - width: number
        - height: number
        +get volume() number
        +validate() string[]
    }
    
    class Delivery {
        - id: number
        - deliveryNumber: string
        - deliveryDate: string
        - timeStart: string
        - timeEnd: string
        - status: string
        - courierId: number
        - vehicleId: number
        - totalWeight: number
        - totalVolume: number
        +get isEditable() boolean
        +calculateTotals() void
    }
    
    class DeliveryPoint {
        - id: number
        - sequence: number
        - latitude: number
        - longitude: number
        - deliveryId: number
        +get coordinates() object
    }
    
    class DeliveryProduct {
        - id: number
        - quantity: number
        - productId: number
        - deliveryPointId: number
    }
    
    %% ===== API Сервисы =====
    class ApiClient {
        +apiRequest(path, options) Promise
        +withRetry(fn) Promise
    }
    
    class DeliveryService {
        +list(token, filters) Promise
        +get(token, id) Promise
        +create(token, data) Promise
        +update(token, id, data) Promise
        +delete(token, id) Promise
        +generate(token, data) Promise
    }
    
    class UserService {
        +list(token, role) Promise
        +create(token, data) Promise
        +update(token, id, data) Promise
        +delete(token, id) Promise
    }
    
    class VehicleService {
        +list(token) Promise
        +create(token, data) Promise
        +update(token, id, data) Promise
        +delete(token, id) Promise
    }
    
    class ProductService {
        +list(token) Promise
        +create(token, data) Promise
        +update(token, id, data) Promise
        +delete(token, id) Promise
    }
    
    class RouteService {
        +calculate(token, points) Promise
    }
    
    %% ===== Контекст =====
    class AuthContext {
        - token: string
        - user: User
        - loading: boolean
        - error: string
        +get isAuthenticated() boolean
        +login(credentials) Promise
        +logout() void
        +refreshSession() Promise
    }
    
    %% ===== Страницы =====
    class LoginPage {
        - form: object
        - loginAttempts: number
        - isLocked: boolean
        +handleSubmit() void
    }
    
    class DeliveriesPage {
        - deliveries: Delivery[]
        - filters: object
        - form: object
        - generationData: object
        +loadDeliveries() void
        +handleSubmit() void
        +handleGenerate() void
    }
    
    class CourierDeliveriesPage {
        - deliveries: Delivery[]
        - filters: object
        - selectedDelivery: Delivery
        +loadDeliveries() void
        +openDelivery() void
    }
    
    class DashboardPage {
        - data: object
        - loading: boolean
        - error: string
        +loadData() void
    }
    
    class UsersPage {
        - users: User[]
        - form: object
        - editingUser: User
        +loadUsers() void
        +handleSubmit() void
        +handleDelete() void
    }
    
    class VehiclesPage {
        - vehicles: Vehicle[]
        - form: object
        - editingVehicle: Vehicle
        +loadVehicles() void
        +handleSubmit() void
    }
    
    class ProductsPage {
        - products: Product[]
        - form: object
        - editingProduct: Product
        +loadProducts() void
        +handleSubmit() void
    }
    
    %% ===== Компоненты =====
    class AppLayout {
        - navItems: object[]
        +render() ReactNode
        +handleLogout() void
    }
    
    class DeliveryDetails {
        - delivery: Delivery
        - onClose: Function
        +render() ReactNode
    }
    
    class RoleGuard {
        - roles: string[]
        - children: ReactNode
        +render() ReactNode
    }
    
    %% ===== Утилиты =====
    class DateUtils {
        +formatDate(value) string
        +formatTime(value) string
        +calculateDateDiff(start, end) object
    }
    
    class NumberUtils {
        +formatNumber(value) string
        +formatCurrency(value) string
        +calculateVolume(l, w, h) number
    }
    
    class Constants {
        +DELIVERY_STATUSES: array
        +USER_ROLES: array
        +ERROR_MESSAGES: object
    }
    
    %% ===== Связи =====
    Delivery "1" *-- "many" DeliveryPoint : contains
    DeliveryPoint "1" *-- "many" DeliveryProduct : contains
    DeliveryProduct "many" --> "1" Product : references
    Delivery "many" --> "1" User : assigned to
    Delivery "many" --> "1" Vehicle : uses
    
    DeliveryService --> ApiClient : uses
    UserService --> ApiClient : uses
    VehicleService --> ApiClient : uses
    ProductService --> ApiClient : uses
    RouteService --> ApiClient : uses
    
    DeliveriesPage --> DeliveryService : uses
    DeliveriesPage --> RouteService : uses
    CourierDeliveriesPage --> DeliveryService : uses
    UsersPage --> UserService : uses
    VehiclesPage --> VehicleService : uses
    ProductsPage --> ProductService : uses
    
    LoginPage --> AuthContext : uses
    DeliveriesPage --> AuthContext : uses
    CourierDeliveriesPage --> AuthContext : uses
    DashboardPage --> AuthContext : uses
    UsersPage --> AuthContext : uses
    VehiclesPage --> AuthContext : uses
    ProductsPage --> AuthContext : uses
    AppLayout --> AuthContext : uses
    RoleGuard --> AuthContext : uses
    
    DeliveriesPage --> DeliveryDetails : renders
    CourierDeliveriesPage --> DeliveryDetails : renders
    AppLayout --> RoleGuard : uses
    
    LoginPage --> DateUtils : uses
    LoginPage --> ValidationUtils : uses
    LoginPage --> Constants : uses
    DeliveriesPage --> DateUtils : uses
    DeliveriesPage --> NumberUtils : uses

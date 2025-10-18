backend run - mvn spring-boot:run
frontend run - npm run dev

# E-Commerce Full-Stack Application

A complete e-commerce application built with Spring Boot (Backend) and React (Frontend) featuring product management, shopping cart functionality, and image handling.

## 🏗️ Project Architecture

This project follows a **3-tier architecture** with clear separation of concerns:

```
├── server/          # Spring Boot Backend
├── client/          # React Frontend
└── README.md        # This documentation
```

## 🚀 Technology Stack

### Backend (Spring Boot)
- **Framework**: Spring Boot 3.5.6
- **Java Version**: Java 21
- **Database**: H2 In-Memory Database
- **ORM**: Spring Data JPA with Hibernate
- **Build Tool**: Maven
- **Additional Libraries**: Lombok, Spring Web, Spring Data JPA

### Frontend (React)
- **Framework**: React 18.2.0
- **Build Tool**: Vite
- **Routing**: React Router DOM
- **HTTP Client**: Axios
- **UI Framework**: Bootstrap 5.3.3
- **State Management**: React Context API

## 📁 Project Structure

### Backend Structure (`server/`)
```
src/main/java/Server/ecom_proj/
├── controller/          # REST API Controllers
│   └── ProductController.java
├── model/              # Entity Classes
│   └── Product.java
├── repository/         # Data Access Layer
│   └── ProductRepo.java
├── service/           # Business Logic Layer
│   └── ProductService.java
└── EcomProjApplication.java  # Main Application Class

src/main/resources/
├── application.properties    # Configuration
└── data.sql                 # Initial Data
```

### Frontend Structure (`client/`)
```
src/
├── components/         # React Components
│   ├── Home.jsx
│   ├── Navbar.jsx
│   ├── Product.jsx
│   ├── Cart.jsx
│   ├── AddProduct.jsx
│   └── UpdateProduct.jsx
├── Context/           # State Management
│   └── Context.jsx
├── App.jsx           # Main App Component
└── axios.jsx         # HTTP Client Configuration
```

## 🔧 Backend Implementation Details

### 1. Entity Layer (`Product.java`)
The `Product` entity represents the core data model with the following features:

```java
@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    private String description;
    private String brand;
    private BigDecimal price;
    private String category;
    private Date releaseDate;
    private boolean productAvailable;
    private int stockQuantity;
    
    // Image handling
    private String imageName;
    private String imageType;
    @Lob
    private byte[] imageDate;
}
```

**Key Features:**
- **Lombok Annotations**: `@Data`, `@AllArgsConstructor`, `@NoArgsConstructor` for automatic getter/setter generation
- **JPA Annotations**: `@Entity`, `@Id`, `@GeneratedValue` for database mapping
- **Image Storage**: Binary image storage using `@Lob` annotation
- **BigDecimal**: Used for precise price calculations

### 2. Repository Layer (`ProductRepo.java`)
```java
@Repository
public interface ProductRepo extends JpaRepository<Product, Integer> {
    @Query("SELECT p from Product p WHERE "+
            "LOWER(p.name) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
            "LOWER(p.description) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
            "LOWER(p.brand) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
            "LOWER(p.category) LIKE LOWER(CONCAT('%', :keyword, '%'))")
    List<Product> searchProducts(String keyword);
}
```

**Features:**
- **Spring Data JPA**: Extends `JpaRepository` for CRUD operations
- **Custom Query**: JPQL query for multi-field search functionality
- **Case-Insensitive Search**: Searches across name, description, brand, and category

### 3. Service Layer (`ProductService.java`)
The service layer handles business logic and image processing:

```java
@Service
public class ProductService {
    @Autowired
    private ProductRepo repo;
    
    public Product addProduct(Product product, MultipartFile imageFile) throws IOException {
        product.setImageName(imageFile.getOriginalFilename());
        product.setImageType(imageFile.getContentType());
        product.setImageDate(imageFile.getBytes());
        return repo.save(product);
    }
}
```

**Key Responsibilities:**
- **Image Processing**: Converts `MultipartFile` to byte array for database storage
- **Business Logic**: Handles product operations
- **Data Validation**: Ensures data integrity before database operations

### 4. Controller Layer (`ProductController.java`)
RESTful API endpoints with comprehensive CRUD operations:

```java
@RestController
@CrossOrigin
@RequestMapping("/api")
public class ProductController {
    
    @GetMapping("/products")
    public ResponseEntity<List<Product>> getAllProducts()
    
    @PostMapping("/product")
    public ResponseEntity<?> addProduct(@RequestPart Product product,
                                        @RequestPart MultipartFile imageFile)
    
    @GetMapping("/product/{productId}/image")
    public ResponseEntity<byte[]> getImageByProductId(@PathVariable int productId)
}
```

**API Endpoints:**
- `GET /api/products` - Retrieve all products
- `GET /api/product/{id}` - Get product by ID
- `POST /api/product` - Create new product with image
- `PUT /api/product/{id}` - Update product
- `DELETE /api/product/{id}` - Delete product
- `GET /api/product/{id}/image` - Get product image
- `GET /api/products/search?keyword=xyz` - Search products

## 🎨 Frontend Implementation Details

### 1. Component Architecture
The React application uses a component-based architecture:

- **Home.jsx**: Product listing and filtering
- **Product.jsx**: Individual product details
- **Cart.jsx**: Shopping cart management
- **AddProduct.jsx**: Product creation form
- **UpdateProduct.jsx**: Product editing form
- **Navbar.jsx**: Navigation and category filtering

### 2. State Management
Uses React Context API for global state management:

```javascript
// Context.jsx
export const AppProvider = ({ children }) => {
  const [products, setProducts] = useState([]);
  const [cart, setCart] = useState([]);
  
  const addToCart = (product) => {
    // Cart logic implementation
  };
  
  return (
    <AppContext.Provider value={{ products, cart, addToCart }}>
      {children}
    </AppContext.Provider>
  );
};
```

### 3. HTTP Communication
Axios configuration for API communication:

```javascript
// axios.jsx
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:8080/api',
  headers: {
    'Content-Type': 'application/json',
  },
});
```

## 🗄️ Database Configuration

### H2 Database Setup
```properties
# application.properties
spring.datasource.url=jdbc:h2:mem:saif
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
```

**Features:**
- **In-Memory Database**: H2 for development and testing
- **Auto Schema Creation**: `ddl-auto=update` creates tables automatically
- **Console Access**: H2 console enabled for database inspection
- **SQL Logging**: Shows generated SQL queries

## 🚀 How to Run the Application

### Prerequisites
- Java 21 or higher
- Node.js 16 or higher
- Maven 3.6 or higher

### Backend Setup
1. Navigate to the `server` directory:
   ```bash
   cd server
   ```

2. Run the Spring Boot application:
   ```bash
   mvn spring-boot:run
   ```
   
   Or use the Maven wrapper:
   ```bash
   ./mvnw spring-boot:run
   ```

3. The backend will start on `http://localhost:8080`

### Frontend Setup
1. Navigate to the `client` directory:
   ```bash
   cd client
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Start the development server:
   ```bash
   npm run dev
   ```

4. The frontend will start on `http://localhost:5173`

## 🔄 Application Flow

### 1. Product Management Flow
```
User Input → React Component → Axios HTTP Request → 
Spring Controller → Service Layer → Repository Layer → 
Database → Response Back to Frontend
```

### 2. Image Handling Flow
```
File Upload → MultipartFile → Service Layer → 
Byte Array Conversion → Database Storage → 
Image Retrieval → Byte Array → HTTP Response
```

### 3. Search Functionality
```
Search Query → Repository Custom Query → 
JPQL Execution → Database Search → 
Filtered Results → Frontend Display
```

## 🛠️ Key Features Implemented

### Backend Features
- ✅ **RESTful API Design**: Complete CRUD operations
- ✅ **Image Storage**: Binary image storage in database
- ✅ **Search Functionality**: Multi-field search across product attributes
- ✅ **CORS Configuration**: Cross-origin resource sharing enabled
- ✅ **Error Handling**: Comprehensive exception handling
- ✅ **Data Validation**: Input validation and error responses

### Frontend Features
- ✅ **Responsive Design**: Bootstrap-based responsive UI
- ✅ **Shopping Cart**: Add/remove products with quantity management
- ✅ **Product Management**: Create, read, update, delete products
- ✅ **Image Display**: Dynamic image loading from backend
- ✅ **Search & Filter**: Real-time product search and category filtering
- ✅ **Routing**: React Router for navigation

## 🔧 Configuration Details

### Maven Dependencies
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

### React Dependencies
```json
{
  "dependencies": {
    "axios": "^1.6.8",
    "bootstrap": "^5.3.3",
    "react": "^18.2.0",
    "react-bootstrap": "^2.10.2",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.23.0"
  }
}
```

## 🐛 Troubleshooting

### Common Issues
1. **Port Conflicts**: Ensure ports 8080 (backend) and 5173 (frontend) are available
2. **CORS Issues**: Backend has `@CrossOrigin` annotation for CORS handling
3. **Image Upload**: Ensure proper `multipart/form-data` content type
4. **Database Connection**: H2 console available at `http://localhost:8080/h2-console`

### Development Tips
- Use H2 console to inspect database tables and data
- Check browser developer tools for API call debugging
- Monitor Spring Boot console for SQL queries and errors
- Use React Developer Tools for component state inspection

## 📈 Future Enhancements

- [ ] User Authentication & Authorization
- [ ] Order Management System
- [ ] Payment Integration
- [ ] Product Categories Management
- [ ] Advanced Search Filters
- [ ] Product Reviews & Ratings
- [ ] Inventory Management
- [ ] Email Notifications
- [ ] Admin Dashboard
- [ ] Mobile Responsive Optimization

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

**Note**: This is a full-stack e-commerce application demonstrating modern web development practices with Spring Boot and React. The application includes complete CRUD operations, image handling, search functionality, and a responsive user interface.

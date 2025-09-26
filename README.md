# BookMartMVC - Online Bookstore E-Commerce Platform

## Overview

BookMartMVC is a full-featured e-commerce web application built with ASP.NET Core MVC for managing and selling books online. It provides a user-friendly interface for browsing books, searching by genre, adding to cart, secure checkout, user authentication, order management, admin dashboard for stock and reports, and an AI-powered chat support system using Google Gemini API.

The application follows the Repository pattern for data access, uses Entity Framework Core for ORM, and ASP.NET Identity for user management. It's designed for scalability and maintainability, with Razor Pages for Identity UI and custom views for core functionality.

Key highlights:
- Responsive UI with Bootstrap.
- AI chat bot for customer queries, order/cart/profile info, and FAQs.
- Admin features for book/genre/stock management and sales reports.
- Database seeding for demo data.

## Features

### User Features
- **Browse & Search**: View books on homepage, search by name, filter by genre.
- **Shopping Cart**: Add/remove books, view cart summary.
- **Checkout**: Fill shipping details, place order.
- **User Authentication**: Register/login with email confirmation, manage profile, view orders.
- **Order History**: Track orders with status (e.g., Pending, Shipped, Delivered).
- **AI Chat Support**: Interactive chat for queries like "Show my orders", "Show my cart", book info, FAQs (e.g., shipping, returns). Powered by Gemini AI, with session-based history.

### Admin Features
- **Dashboard**: Overview of orders, top-selling books.
- **Book Management**: Add/update books with images, genres, pricing.
- **Genre Management**: CRUD for book categories.
- **Stock Management**: Track and update book inventory.
- **Order Management**: View all orders, update status, view details.
- **Reports**: Generate top 5 selling books report.

### Technical Features
- **Data Layer**: EF Core with SQL Server, migrations for schema changes, DbSeeder for initial data (books, genres, users, roles).
- **Security**: ASP.NET Identity with roles (User, Admin), HTTPS redirection, anti-forgery tokens.
- **Session Management**: In-memory cache for cart and chat history (30-min timeout).
- **File Uploads**: Image handling for book covers via FileService.
- **Error Handling**: Custom error pages, database exception filters.

## Tech Stack

- **Backend**: ASP.NET Core 9.0 MVC, C# 12
- **Database**: Entity Framework Core 9.0, SQL Server (configurable to SQLite for dev)
- **Frontend**: Razor Views, Bootstrap 5 CSS/JS, jQuery, Validation
- **Authentication**: ASP.NET Identity with Entity Framework stores
- **AI Integration**: Google Gemini API (Generative Language) for chat bot
- **Other**: Newtonsoft.Json for API calls, AutoMapper (if extended), Session Middleware

Dependencies (from BookMart.csproj):
- Microsoft.AspNetCore.Identity.EntityFrameworkCore (9.0.9)
- Microsoft.EntityFrameworkCore.SqlServer (9.0.9)
- Microsoft.EntityFrameworkCore.Tools (9.0.9)

## Prerequisites

- **.NET 9.0 SDK**: Download from [Microsoft](https://dotnet.microsoft.com/download/dotnet/9.0)
- **SQL Server**: Local instance (e.g., SQL Server Express) or Azure SQL. Connection string in `appsettings.json`.
- **GitHub CLI (gh)**: For repo interactions (optional, already installed).
- **Google Gemini API Key**: For chat bot. Add to `appsettings.json` under `Gemini:ApiKey`.
- **Visual Studio 2022** or **VS Code** with C# extension for development.

## Installation & Setup

1. **Clone the Repository**:
   ```
   git clone https://github.com/meetmehta136/Book-Mart-MVC.git
   cd Book-Mart-MVC
   ```

2. **Restore Dependencies**:
   ```
   dotnet restore
   ```

3. **Configure appsettings.json**:
   - Update `ConnectionStrings:DefaultConnection` to your SQL Server (e.g., `Server=(localdb)\\mssqllocaldb;Database=BookMartDb;Trusted_Connection=true;TrustServerCertificate=true;`).
   - Add Gemini API: `Gemini:ApiKey = "your-api-key"`.

4. **Apply Migrations & Seed Data**:
   ```
   dotnet ef migrations add InitialCreate  # If needed, but migrations are pre-included
   dotnet ef database update
   ```
   - Run the app once to trigger DbSeeder (seeds books, genres, admin user: Email=admin@bookmart.com, Password=Admin@123).

5. **Build & Run**:
   ```
   dotnet build
   dotnet run
   ```
   - Access at `https://localhost:7xxx` (port from launchSettings.json).
   - In development, use `dotnet watch run` for hot reload.

## Usage

### Browsing Books
- Home page (`/Home/Index`): Search books by name (`sterm`), filter by genre ID.
- View book details, add to cart.

### User Flow
1. Register/Login at `/Identity/Account/Register` or `/Identity/Account/Login`.
2. Browse/Add to Cart → `/Cart/GetUserCart`.
3. Checkout → `/Cart/Checkout` → Fill details → Place order.
4. Success → Order created, view at `/UserOrder/UserOrders`.
5. Chat: `/Home/Chat` for support (e.g., "Show my orders", "How to return?").

### Admin Flow
- Login as Admin (seeded or create role).
- Dashboard: `/AdminOperations/Dashboard`.
- Manage Books: `/Book/AddBook`, `/Book/UpdateBook`.
- Manage Stock: `/Stock/ManageStock`.
- View Orders: `/AdminOperations/AllOrders`, update status.
- Reports: `/Reports/TopFiveSellingBooks`.

### AI Chat Bot
- Integrated in HomeController using Gemini API.
- Handles: Order/cart/profile queries (requires login), book FAQs, general help.
- Session-based history, clear via `/Home/ClearChat`.
- Knowledge base from DB (books/genres) + hardcoded FAQs.

## Project Structure

```
BookMartMVC/
├── Areas/Identity/          # ASP.NET Identity Razor Pages (login, register, manage)
├── Constants/               # Enums: PaymentMethods.cs, Roles.cs
├── Controllers/             # MVC Controllers
│   ├── HomeController.cs    # Homepage, search, chat bot
│   ├── BookController.cs    # Book CRUD
│   ├── CartController.cs    # Cart, checkout, payment
│   ├── GenreController.cs   # Genre management
│   ├── AdminOperationsController.cs # Admin dashboard, orders
│   ├── StockController.cs   # Stock management
│   ├── ReportsController.cs # Sales reports
│   └── UserOrderController.cs # User orders
├── Data/                    # EF Core
│   ├── ApplicationDbContext.cs # DbContext with entities
│   ├── DbSeeder.cs          # Seed method for demo data
│   └── Migrations/          # DB migrations (e.g., added-tables, booktable)
├── Models/                  # Entities & DTOs
│   ├── Book.cs              # Book entity (name, author, price, genre, stock)
│   ├── Order.cs             # Order entity (user, status, details, payment)
│   ├── Genre.cs             # Genre entity
│   ├── Stock.cs             # Inventory
│   ├── CartDetail.cs        # Cart items
│   ├── OrderDetail.cs       # Order items
│   ├── OrderStatus.cs       # Order statuses
│   ├── ShoppingCart.cs      # Cart aggregate
│   └── DTOs/                # View models (BookDTO, CheckoutModel, etc.)
├── Repositories/            # Repository pattern interfaces/impl
│   ├── IBookRepository.cs   # Book ops
│   ├── BookRepository.cs
│   ├── ICartRepository.cs   # Cart ops
│   ├── CartRepository.cs
│   ├── IGenreRepository.cs  # Genre ops
│   ├── GenreRepository.cs
│   ├── IUserOrderRepository.cs # User orders
│   ├── UserOrderRepository.cs
│   ├── IStockRepository.cs  # Stock
│   ├── StockRepository.cs
│   ├── IHomeRepository.cs   # Home/search
│   ├── HomeRepository.cs
│   └── IReportRepository.cs # Reports
├── Shared/                  # Shared services
│   └── FileService.cs       # File upload for images
├── Views/                   # Razor views
│   ├── Home/                # Index.cshtml (books list), Chat.cshtml (AI chat)
│   ├── Book/                # AddBook.cshtml, Index.cshtml
│   ├── Cart/                # Checkout.cshtml, Payment.cshtml
│   ├── AdminOperations/     # AllOrders.cshtml, Dashboard.cshtml
│   ├── Reports/             # TopFiveSellingBooks.cshtml
│   ├── Shared/              # _Layout.cshtml (Bootstrap layout), _AdminLayout.cshtml
│   └── _ViewImports.cshtml
├── wwwroot/                 # Static assets
│   ├── css/                 # Bootstrap.min.css
│   ├── img/                 # Book images (e.g., NoImage.png)
│   ├── lib/                 # jQuery, Bootstrap JS, licenses
│   └── favicon.ico
├── Program.cs               # App startup, services, routing, seeder
├── BookMart.csproj          # Project file
├── appsettings.json         # Config (connection, Gemini)
├── GlobalUsings.cs          # Global usings
└── .gitignore               # Ignores bin/obj, appsettings.json, images
```

## Deployment

- **Azure/IIS**: Publish via `dotnet publish`, configure web.config for SQL connection.
- **Database**: Run migrations on production DB.
- **Gemini API**: Ensure API key is secure (Azure Key Vault).
- **HTTPS**: Enforce in production.
- **Scaling**: Use Redis for distributed session if multi-instance.

For Docker: Create Dockerfile with multi-stage build, expose port 80.

## Screenshots

(Descriptions, as no images generated)
- **Homepage**: Book grid with search bar, genre filters, "Add to Cart" buttons.
- **Cart/Checkout**: Item list, total, shipping form.
- **Admin Dashboard**: Order table, charts for top sellers.
- **Chat**: Conversation interface with bot responses (e.g., "Your cart has 2 books totaling $25").

## Contributing

1. Fork the repo.
2. Create feature branch: `git checkout -b feature/amazing-feature`.
3. Commit changes: `git commit -m "Add amazing feature"`.
4. Push: `git push origin feature/amazing-feature`.
5. Open Pull Request (prefix: blackboxai/ if from AI changes).

Report issues or suggest features via GitHub Issues. Follow C# conventions, add tests for new repos.

## Contributors

- **Prit Pastagiya** - Project Lead, Backend Development
- **Hardi** - Frontend Development, UI/UX Design

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details. (Add if needed.)

## Acknowledgments

- ASP.NET Core Team
- Entity Framework Core
- Google Gemini API
- Bootstrap for UI

For support, contact meetmehta136@gmail.com or open an issue.

---

*Last Updated: September 2025*

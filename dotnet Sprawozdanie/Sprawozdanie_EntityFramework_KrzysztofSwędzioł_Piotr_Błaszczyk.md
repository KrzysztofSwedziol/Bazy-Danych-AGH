### Imię i nazwisko autorów : Krzysztof Swędzioł i Piotr Błaszczyk

## I część zadania 

using System;

Console. WriteLine ("Podaj nazwę produktu: ");
String? prodName = Console.ReadLine () ;
ProdContext prodContext = new ProdContext();
Product product = new Product { ProductName = prodName};
prodContext.Products.Add(product) ;
prodContext.SaveChanges();

var query = from prod in prodContext.Products
            select prod.ProductName;

foreach (var pName in query)
{
Console.WriteLine(pName);
}


using Microsoft.EntityFrameworkCore;
public class ProdContext : DbContext
{
public DbSet<Product> Products { get; set; }
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
base.OnConfiguring(optionsBuilder);
optionsBuilder.UseSqlite("Datasource=MyProductDatabase");
}
}

public class Product
{
public int ProductID { get; set; }
public String? ProductName { get; set; } public
int UnitsInStock { get; set; }
public int SupplierID { get; set; } // Foreign key
public Supplier? Supplier { get; set; }
}

## II Część
klasa dostawcy : 

public class Supplier
{
    public int SupplierID { get; set; }
    public string? CompanyName { get; set; }
    public string? Street { get; set; }
    public string? City { get; set; }
}
 

Modyfikacja do Prod Context : 
using Microsoft.EntityFrameworkCore;
public class ProdContext : DbContext
{
    public DbSet<Product> Products { get; set; }
    public DbSet<Supplier> Suppliers { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    
    {
        base.OnConfiguring(optionsBuilder);
        optionsBuilder.UseSqlite("Datasource=MyProductDatabase");
    }
}

modyfikacja do Product : 
public class Product
{
    public int ProductID { get; set; }
    public String? ProductName { get; set; } public
    int UnitsInStock { get; set; }
    public int SupplierID { get; set; } // Foreign key
    public Supplier? Supplier { get; set; }
}

modyfikacja programu : 
using System;
using System.Linq;
using Microsoft.EntityFrameworkCore;

using System;
using System.Linq;
using Microsoft.EntityFrameworkCore;

class Program
{
    static void Main(string[] args)
    {
        using (ProdContext context = new ProdContext())
        {
            Console.WriteLine("Enter the supplier name:");
            string supplierName = Console.ReadLine()?.Trim();

            if (string.IsNullOrEmpty(supplierName))
            {
                Console.WriteLine("Supplier name cannot be empty.");
                return;
            }

            Supplier? supplier = context.Suppliers
                                        .FirstOrDefault(s => s.CompanyName == supplierName);

            if (supplier == null)
            {
                supplier = new Supplier { CompanyName = supplierName };
                context.Suppliers.Add(supplier);
                context.SaveChanges(); 
            }

            Console.WriteLine("Enter product name:");
            string productName = Console.ReadLine()?.Trim();

            if (string.IsNullOrEmpty(productName))
            {
                Console.WriteLine("Product name cannot be empty.");
                return;
            }

            if (supplier != null)
            {
                Product product = new Product { ProductName = productName, UnitsInStock = 10, SupplierID = supplier.SupplierID };
                context.Products.Add(product);
                context.SaveChanges();
            }

            Console.WriteLine("\nAll Products and Their Suppliers:");
            var products = context.Products.Include(p => p.Supplier);
            foreach (var p in products)
            {
                Console.WriteLine($"{p.ProductName} supplied by {p.Supplier?.CompanyName}");
            }
        }
    }
}

wynik działania dodania krzesła przez suppliera MarcinStore : 

![alt text](<dotnet screeny/MarcinStore krzeslo.png>)
![alt text](<dotnet screeny/zadanie 1.png>)
b) Do klasy Supplier dodana została lista produktów jakie dany dostawca dostarcza. Dodatkowo usunięto suppliera Product

public class Supplier
{
    public int SupplierID { get; set; }
    public string? CompanyName { get; set; }
    public string? Street { get; set; }
    public string? City { get; set; }
    public List<Product> Products { get; set; } = new List<Product>();  
}

public class Product
{
    public int ProductID { get; set; }
    public string? ProductName { get; set; }
    public int UnitsInStock { get; set; }
}

using System;
using System.Linq;
using Microsoft.EntityFrameworkCore;

class Program
{
    static void Main(string[] args)
    {
        using (ProdContext context = new ProdContext())
        {
            Console.WriteLine("Enter the supplier name:");
            string supplierName = Console.ReadLine()?.Trim();
            if (string.IsNullOrEmpty(supplierName))
            {
                Console.WriteLine("Supplier name cannot be empty.");
                return;
            }
            Supplier supplier = context.Suppliers
                                       .Include(s => s.Products)  
                                       .FirstOrDefault(s => s.CompanyName == supplierName);

            if (supplier == null)
            {
                supplier = new Supplier { CompanyName = supplierName };
                context.Suppliers.Add(supplier);
                context.SaveChanges();
            }

            Console.WriteLine("Enter product name:");
            string productName = Console.ReadLine()?.Trim();
            if (string.IsNullOrEmpty(productName))
            {
                Console.WriteLine("Product name cannot be empty.");
                return;
            }

            Product product = new Product { ProductName = productName, UnitsInStock = 10 };
            supplier.Products.Add(product);
            context.SaveChanges();

            Console.WriteLine("\nAll Products and Their Suppliers:");
            foreach (var sup in context.Suppliers.Include(s => s.Products))
            {
                Console.WriteLine($"Supplier: {sup.CompanyName}");
                foreach (var p in sup.Products)
                {
                    Console.WriteLine($"- {p.ProductName}");
                }
            }
        }
    }
}
wyniki działania : 
![alt text](<dotnet screeny/PiotrekStorePatelnia.png>)
![alt text](<dotnet screeny/zadanie1b.png>)
c)
public class Product
{
    public int ProductID { get; set; }
    public string? ProductName { get; set; }
    public int UnitsInStock { get; set; }
    public int SupplierID { get; set; }  
    public Supplier? Supplier { get; set; } 
}
Do klasy Product dodałem Dostawcę



using System;
using System.Linq;
using Microsoft.EntityFrameworkCore;

class Program
{
    static void Main(string[] args)
    {
        using (ProdContext context = new ProdContext())
        {
            Console.WriteLine("Enter the supplier name:");
            string supplierName = Console.ReadLine()?.Trim();
            if (string.IsNullOrEmpty(supplierName))
            {
                Console.WriteLine("Supplier name cannot be empty.");
                return;
            }
            Supplier supplier = context.Suppliers
                                       .Include(s => s.Products)  
                                       .FirstOrDefault(s => s.CompanyName == supplierName);

            if (supplier == null)
            {
                supplier = new Supplier { CompanyName = supplierName };
                context.Suppliers.Add(supplier);
                context.SaveChanges();
            }

            Console.WriteLine("Enter product name:");
            string productName = Console.ReadLine()?.Trim();
            if (string.IsNullOrEmpty(productName))
            {
                Console.WriteLine("Product name cannot be empty.");
                return;
            }


            Product product = new Product { ProductName = productName, UnitsInStock = 10, Supplier = supplier };
            context.Products.Add(product);
            context.SaveChanges();

            Console.WriteLine("\nAll Products and Their Suppliers:");
            foreach (var sup in context.Suppliers.Include(s => s.Products))
            {
                Console.WriteLine($"Supplier: {sup.CompanyName}");
                foreach (var p in sup.Products)
                {
                    Console.WriteLine($"- {p.ProductName} (Stock: {p.UnitsInStock})");
                }
            }
        }
    }
}

wyniki działania : 
![alt text](<dotnet screeny/KrzysiekStore zadc.png>)

d)
using System.Collections.Generic;

namespace EntityFrameworkExample
{
    public class Supplier
    {
        public int SupplierID { get; set; }
        public string? CompanyName { get; set; }
        public string? Street { get; set; }
        public string? City { get; set; }
        public List<Product> Products { get; set; } = new List<Product>();
    }
}

using System.Collections.Generic;

namespace EntityFrameworkExample
{
    public class Product
    {
        public int ProductID { get; set; }
        public string? ProductName { get; set; }
        public int UnitsInStock { get; set; }
        public int SupplierID { get; set; }  
        public Supplier? Supplier { get; set; }
        public List<ProductInvoice> ProductInvoices { get; set; } = new List<ProductInvoice>();

        public bool Sell(int quantity)
        {
            if (quantity > UnitsInStock)
            {
                Console.WriteLine("Not enough stock available.");
                return false;  
            }
            UnitsInStock -= quantity;
            Console.WriteLine($"{quantity} units of {ProductName} have been sold. Remaining stock: {UnitsInStock}");
            return true;
        }
    }
}

using System.Collections.Generic;

namespace EntityFrameworkExample
{
    public class Invoice
    {
        public int InvoiceID { get; set; }
        public string InvoiceNumber { get; set; }
        public List<ProductInvoice> ProductInvoices { get; set; } = new List<ProductInvoice>();

        public void AddProduct(Product product, int quantity)
        {
            if (product.Sell(quantity))  
            {
                ProductInvoice newInvoiceProduct = new ProductInvoice
                {
                    Product = product,
                    Invoice = this,
                    Quantity = quantity
                };
                ProductInvoices.Add(newInvoiceProduct);
                Console.WriteLine($"Added {quantity} units of {product.ProductName} to invoice {InvoiceNumber}.");
            }
        }
    }
}

Po drodze coś nam się zepsuło i cały projekt się usunął ;(

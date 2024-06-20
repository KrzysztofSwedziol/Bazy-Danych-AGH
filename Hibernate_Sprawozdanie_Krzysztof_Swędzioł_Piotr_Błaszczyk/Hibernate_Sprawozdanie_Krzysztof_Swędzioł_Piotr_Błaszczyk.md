Krzysztof Swędzioł i Piotr Błaszczyk Hibernate Sprawozdanie

zadanie 1

package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();


        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();


        Supplier supplier = new Supplier("KFC", "Rockerfeller Street", "New York");
        session.save(supplier);


        Product product = new Product("Fried Chicken", 10, supplier);
        session.save(product);

        Product product2 = new Product("Quessadila", 5, supplier);
        session.save(product2);


        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}

package org.example;

import jakarta.persistence.*;
import java.util.List;

@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String companyName;
    private String street;
    private String city;

    @OneToMany(mappedBy = "supplier", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<Product> products;

    public Supplier() {
    }

    public Supplier(String companyName, String street, String city) {
        this.companyName = companyName;
        this.city = city;
        this.street = street;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public String getStreet() {
        return street;
    }

    public String getCity() {
        return city;
    }

    public List<Product> getProducts() {
        return products;
    }

    public void setProducts(List<Product> products) {
        this.products = products;
    }
}

package org.example;

import jakarta.persistence.*;
import org.example.Supplier;
import java.util.List;

@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int productID;
    public String ProductName;
    private int unitsInStock;

    @ManyToOne
    @JoinColumn(name = "SupplierID")
    private Supplier supplier;

    public Product() {
    }

    public Product(String productName, int unitsInStock, Supplier supplier) {
        this.ProductName = productName;
        this.unitsInStock = unitsInStock;
        this.supplier = supplier;
    }

    // Getters and setters
    public void setSupplier(Supplier supplier) {
        this.supplier = supplier;
    }

    public Supplier getSupplier() {
        return supplier;
    }
}

wyniki dodania produktów i ich suppliera : 
![alt text](<HibernateScreenshots/zad1 command.png>)
![alt text](<HibernateScreenshots/zad1 output.png>)

zadanie 2

package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();


        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        Product product = new Product("Chips", 20);
        session.save(product);


        Supplier supplier = new Supplier("Lays", "Krakowska", "Kraków", product);
        session.save(supplier);

        Supplier supplier2 = new Supplier("Crunchips", "Warszawska", "Warszawa", product);
        session.save(supplier2);

        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}

package org.example;

import jakarta.persistence.*;
import java.util.List;

@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;
    private String street;
    private String city;

    @ManyToOne
    @JoinColumn(name = "ProductID")
    private Product product;

    public Supplier() {
    }

    public Supplier(String companyName, String street, String city, Product product) {
        this.companyName = companyName;
        this.city = city;
        this.street = street;
        this.product = product;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public String getStreet() {
        return street;
    }

    public String getCity() {
        return city;
    }

}

    package org.example;

    import jakarta.persistence.*;
    import org.example.Supplier;
    import java.util.List;

    @Entity
    public class Product {
        @Id
        @GeneratedValue(strategy = GenerationType.AUTO)
        private int productID;
        public String ProductName;
        private int unitsInStock;

        @OneToMany(mappedBy = "product", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
        private List<Supplier> suppliers;

        public Product() {
        }

        public Product(String productName, int unitsInStock) {
            this.ProductName = productName;
            this.unitsInStock = unitsInStock;
        }

    }

wyniki dodania chipsów i kilku dostawców tego rodzaju produktów : 
![alt text](<HibernateScreenshots/zad2 command.png>)
![alt text](<HibernateScreenshots/zad2 output.png>)

zadanie 3

package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        Product product1 = new Product("Chips", 20);
        Product product2 = new Product("Energy Drink", 30);
        Product product3 = new Product("Cookies", 40);

        Supplier supplier1 = new Supplier("Lays", "Krakowska", "Kraków");
        Supplier supplier2 = new Supplier("Crunchips", "Warszawska", "Warszawa");
        Supplier supplier3 = new Supplier("Monster", "Olkuska", "Kraków");
        Supplier supplier4 = new Supplier("Black", "Warszawska", "Warszawa");
        Supplier supplier5 = new Supplier("Oshee", "Grovestreet", "Los Santos");
        Supplier supplier6 = new Supplier("Oreo", "Kazimierska", "Wrocław");


        product1.getSuppliers().add(supplier1);
        product1.getSuppliers().add(supplier2);
        product1.getSuppliers().add(supplier3);

        product2.getSuppliers().add(supplier3);
        product2.getSuppliers().add(supplier4);
        product2.getSuppliers().add(supplier5);
        product2.getSuppliers().add(supplier1);
        product2.getSuppliers().add(supplier2);

        product3.getSuppliers().add(supplier6);
        product3.getSuppliers().add(supplier2);

        supplier1.getProducts().add(product1);
        supplier1.getProducts().add(product2);

        supplier2.getProducts().add(product3);
        supplier2.getProducts().add(product1);
        supplier2.getProducts().add(product2);

        supplier3.getProducts().add(product2);
        supplier3.getProducts().add(product1);


        supplier4.getProducts().add(product2);
        supplier4.getProducts().add(product3);

        supplier5.getProducts().add(product2);

        supplier6.getProducts().add(product3);

        // Save entities
        session.save(product1);
        session.save(product2);
        session.save(product3);

        session.save(supplier1);
        session.save(supplier2);
        session.save(supplier3);
        session.save(supplier4);
        session.save(supplier5);
        session.save(supplier6);

        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}

package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;
    private String street;
    private String city;

    @ManyToMany
    @JoinTable(
            name = "supplier_product",
            joinColumns = @JoinColumn(name = "supplier_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Supplier() {
    }

    public Supplier(String companyName, String street, String city) {
        this.companyName = companyName;
        this.city = city;
        this.street = street;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public String getStreet() {
        return street;
    }

    public String getCity() {
        return city;
    }
    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }

}

        package org.example;

        import jakarta.persistence.*;
        import org.example.Supplier;

        import java.util.HashSet;
        import java.util.List;
        import java.util.Set;

        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.AUTO)
            private int productID;
            public String ProductName;
            private int unitsInStock;

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Supplier> suppliers = new HashSet<>();

            public Product() {
            }

            public Product(String productName, int unitsInStock) {
                this.ProductName = productName;
                this.unitsInStock = unitsInStock;
            }
            public Set<Supplier> getSuppliers() {
                return this.suppliers;
            }

            public void setSuppliers(Set<Supplier> suppliers) {
                this.suppliers = suppliers;
            }

        }
![alt text](<HibernateScreenshots/zad3 Suppliers.png>)
![alt text](<HibernateScreenshots/zad3 Products.png>)
![alt text](<HibernateScreenshots/zad3 output.png>)

zadanie 4

package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        Category category1 = new Category("Snacks");
        Category category2 = new Category("Drinks");
        Category category3 = new Category("Sweets");

        Product product1 = new Product("Chips", 20, category1);
        Product product2 = new Product("Energy Drink", 30, category2);
        Product product3 = new Product("Cookies", 40, category3);
        Product product4 = new Product("Juice", 60, category2);
        Product product5 = new Product("Water", 100, category2);

        Supplier supplier1 = new Supplier("Lays", "Krakowska", "Kraków");
        Supplier supplier2 = new Supplier("Crunchips", "Warszawska", "Warszawa");
        Supplier supplier3 = new Supplier("Monster", "Olkuska", "Kraków");
        Supplier supplier4 = new Supplier("Black", "Warszawska", "Warszawa");
        Supplier supplier5 = new Supplier("Oshee", "Grovestreet", "Los Santos");
        Supplier supplier6 = new Supplier("Oreo", "Kazimierska", "Wrocław");


        product1.getSuppliers().add(supplier1);
        product1.getSuppliers().add(supplier2);
        product1.getSuppliers().add(supplier3);

        product2.getSuppliers().add(supplier3);
        product2.getSuppliers().add(supplier4);
        product2.getSuppliers().add(supplier5);
        product2.getSuppliers().add(supplier1);
        product2.getSuppliers().add(supplier2);

        product3.getSuppliers().add(supplier6);
        product3.getSuppliers().add(supplier2);

        supplier1.getProducts().add(product1);
        supplier1.getProducts().add(product2);

        supplier2.getProducts().add(product3);
        supplier2.getProducts().add(product1);
        supplier2.getProducts().add(product2);

        supplier3.getProducts().add(product2);
        supplier3.getProducts().add(product1);


        supplier4.getProducts().add(product2);
        supplier4.getProducts().add(product3);

        supplier5.getProducts().add(product2);

        supplier6.getProducts().add(product3);

        // Save entities
        session.save(category1);
        session.save(category2);
        session.save(category3);

        session.save(product1);
        session.save(product2);
        session.save(product3);
        session.save(product4);
        session.save(product5);

        session.save(supplier1);
        session.save(supplier2);
        session.save(supplier3);
        session.save(supplier4);
        session.save(supplier5);
        session.save(supplier6);

        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}

package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;
    private String street;
    private String city;

    @ManyToMany
    @JoinTable(
            name = "supplier_product",
            joinColumns = @JoinColumn(name = "supplier_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Supplier() {
    }

    public Supplier(String companyName, String street, String city) {
        this.companyName = companyName;
        this.city = city;
        this.street = street;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public String getStreet() {
        return street;
    }

    public String getCity() {
        return city;
    }
    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }

}

        package org.example;

        import jakarta.persistence.*;
        import org.example.Supplier;

        import java.util.HashSet;
        import java.util.List;
        import java.util.Set;

        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.AUTO)
            private int productID;
            public String ProductName;
            private int unitsInStock;

            @ManyToOne
            @JoinColumn(name = "category_id")
            private Category category;

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Supplier> suppliers = new HashSet<>();

            public Product() {
            }

            public Product(String productName, int unitsInStock, Category category) {
                this.ProductName = productName;
                this.unitsInStock = unitsInStock;
                this.category = category;
            }
            public Set<Supplier> getSuppliers() {
                return this.suppliers;
            }

            public void setSuppliers(Set<Supplier> suppliers) {
                this.suppliers = suppliers;
            }

        }
        package org.example;

import jakarta.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
public class Category {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int categoryID;
    private String name;

    @OneToMany(mappedBy = "category", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Set<Product> products = new HashSet<>();

    public Category(){

    }
    public Category(String name){
        this.name = name;
    }
}


![alt text](<HibernateScreenshots/Zadanie4 Category.png>)
![alt text](<HibernateScreenshots/zadanie4 output.png>)

Zadanie 5
package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        Category category1 = new Category("Snacks");
        Category category2 = new Category("Drinks");
        Category category3 = new Category("Sweets");

        Product product1 = new Product("Chips", 20, category1);
        Product product2 = new Product("Energy Drink", 30, category2);
        Product product3 = new Product("Cookies", 40, category3);
        Product product4 = new Product("Juice", 60, category2);
        Product product5 = new Product("Water", 100, category2);

        Invoice invoice1 = new Invoice(1, 2);
        Invoice invoice2 = new Invoice(2, 3);

        invoice1.getProducts().add(product1);
        invoice1.getProducts().add(product2);

        invoice2.getProducts().add(product2);
        invoice2.getProducts().add(product3);


        product1.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice2);
        product3.getInvoices().add(invoice2);

        Supplier supplier1 = new Supplier("Lays", "Krakowska", "Kraków");
        Supplier supplier2 = new Supplier("Crunchips", "Warszawska", "Warszawa");
        Supplier supplier3 = new Supplier("Monster", "Olkuska", "Kraków");
        Supplier supplier4 = new Supplier("Black", "Warszawska", "Warszawa");
        Supplier supplier5 = new Supplier("Oshee", "Grovestreet", "Los Santos");
        Supplier supplier6 = new Supplier("Oreo", "Kazimierska", "Wrocław");


        product1.getSuppliers().add(supplier1);
        product1.getSuppliers().add(supplier2);
        product1.getSuppliers().add(supplier3);

        product2.getSuppliers().add(supplier3);
        product2.getSuppliers().add(supplier4);
        product2.getSuppliers().add(supplier5);
        product2.getSuppliers().add(supplier1);
        product2.getSuppliers().add(supplier2);

        product3.getSuppliers().add(supplier6);
        product3.getSuppliers().add(supplier2);

        supplier1.getProducts().add(product1);
        supplier1.getProducts().add(product2);

        supplier2.getProducts().add(product3);
        supplier2.getProducts().add(product1);
        supplier2.getProducts().add(product2);

        supplier3.getProducts().add(product2);
        supplier3.getProducts().add(product1);


        supplier4.getProducts().add(product2);
        supplier4.getProducts().add(product3);

        supplier5.getProducts().add(product2);

        supplier6.getProducts().add(product3);

        // Save entities
        session.save(category1);
        session.save(category2);
        session.save(category3);

        session.save(product1);
        session.save(product2);
        session.save(product3);
        session.save(product4);
        session.save(product5);

        session.save(supplier1);
        session.save(supplier2);
        session.save(supplier3);
        session.save(supplier4);
        session.save(supplier5);
        session.save(supplier6);

        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}


package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;
    private String street;
    private String city;

    @ManyToMany
    @JoinTable(
            name = "supplier_product",
            joinColumns = @JoinColumn(name = "supplier_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Supplier() {
    }

    public Supplier(String companyName, String street, String city) {
        this.companyName = companyName;
        this.city = city;
        this.street = street;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public String getStreet() {
        return street;
    }

    public String getCity() {
        return city;
    }
    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }

}

        package org.example;

        import jakarta.persistence.*;
        import org.example.Supplier;

        import java.util.HashSet;
        import java.util.List;
        import java.util.Set;

        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.AUTO)
            private int productID;
            public String ProductName;
            private int unitsInStock;

            @ManyToOne
            @JoinColumn(name = "category_id")
            private Category category;

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Supplier> suppliers = new HashSet<>();

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Invoice> invoices = new HashSet<>();

            public Product() {
            }

            public Product(String productName, int unitsInStock, Category category) {
                this.ProductName = productName;
                this.unitsInStock = unitsInStock;
                this.category = category;
            }
            public Set<Supplier> getSuppliers() {
                return this.suppliers;
            }

            public void setSuppliers(Set<Supplier> suppliers) {
                this.suppliers = suppliers;
            }
            public Set<Invoice> getInvoices() {
                return invoices;
            }

            public void setInvoices(Set<Invoice> invoices) {
                this.invoices = invoices;
            }

        }

package org.example;

import jakarta.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
public class Category {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int categoryID;
    private String name;

    @OneToMany(mappedBy = "category", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Set<Product> products = new HashSet<>();

    public Category(){

    }
    public Category(String name){
        this.name = name;
    }
}

package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.Set;

@Entity
public class Invoice {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int invoiceID;
    private int invoiceNumber;
    private int quantity;

    @ManyToMany
    @JoinTable(
            name = "invoice_product",
            joinColumns = @JoinColumn(name = "invoice_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Invoice() {
    }

    public Invoice(int invoiceNumber, int quantity) {
        this.invoiceNumber = invoiceNumber;
        this.quantity = quantity;
    }

    // Getters and setters
    public int getId() {
        return invoiceID;
    }

    public void setId(int id) {
        this.invoiceID = id;
    }

    public int getInvoiceNumber() {
        return this.invoiceNumber;
    }

    public void setInvoiceNumber(int invoiceNumber) {
        this.invoiceNumber = invoiceNumber;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }
}
![alt text](<HibernateScreenshots/zadanie5 invoice.png>)
![alt text](<HibernateScreenshots/zadanie5 output.png>)

zadanie 6
package org.example;

import jakarta.persistence.EntityManager;
import jakarta.persistence.EntityManagerFactory;
import jakarta.persistence.EntityTransaction;
import jakarta.persistence.Persistence;

public class newMain {

    private static EntityManagerFactory entityManagerFactory = null;

    public static void main(String[] args) {
        entityManagerFactory = Persistence.createEntityManagerFactory("example-unit");

        EntityManager entityManager = entityManagerFactory.createEntityManager();
        EntityTransaction tx = entityManager.getTransaction();
        tx.begin();

        Category cat1 = new Category("Snacks");
        Category cat2 = new Category("Drinks");
        Category cat3 = new Category("Sweets");

        Product product1 = new Product("Chips", 20, cat1);
        Product product2 = new Product("Energy Drink", 30, cat2);
        Product product3 = new Product("Cookies", 40, cat3);


        Invoice invoice1 = new Invoice(1, 2);
        Invoice invoice2 = new Invoice(2, 3);


        invoice1.getProducts().add(product1);
        invoice1.getProducts().add(product2);

        invoice2.getProducts().add(product2);
        invoice2.getProducts().add(product3);


        product1.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice2);
        product3.getInvoices().add(invoice2);


        entityManager.persist(cat1);
        entityManager.persist(cat2);
        entityManager.persist(cat3);

        entityManager.persist(product1);
        entityManager.persist(product2);
        entityManager.persist(product3);
        entityManager.persist(invoice1);
        entityManager.persist(invoice2);

        tx.commit();


        System.out.println("Products sold in invoice 1:");
        Invoice fetchedInvoice = entityManager.find(Invoice.class, invoice1.getId());
        for (Product product : fetchedInvoice.getProducts()) {
            System.out.println(product.getProductName());
        }

        System.out.println("Invoices containing product 'Energy Drink':");
        Product fetchedProduct = entityManager.find(Product.class, product2.getId());
        for (Invoice invoice : fetchedProduct.getInvoices()) {
            System.out.println(invoice.getInvoiceNumber());
        }

        entityManager.close();
        entityManagerFactory.close();
    }
}


        package org.example;

        import jakarta.persistence.*;
        import org.example.Supplier;

        import java.util.HashSet;
        import java.util.List;
        import java.util.Set;

        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.AUTO)
            private int productID;
            public String productName;
            private int unitsInStock;

            @ManyToOne
            @JoinColumn(name = "category_id")
            private Category category;

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Supplier> suppliers = new HashSet<>();

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Invoice> invoices = new HashSet<>();

            public Product() {
            }

            public Product(String productName, int unitsInStock, Category category) {
                this.productName = productName;
                this.unitsInStock = unitsInStock;
                this.category = category;
            }

            public int getId() {
                return productID;
            }

            public void setId(int id) {
                this.productID = id;
            }

            public String getProductName() {
                return productName;
            }

            public void setProductName(String productName) {
                this.productName = productName;
            }
            public Set<Supplier> getSuppliers() {
                return this.suppliers;
            }

            public void setSuppliers(Set<Supplier> suppliers) {
                this.suppliers = suppliers;
            }
            public Set<Invoice> getInvoices() {
                return invoices;
            }

            public void setInvoices(Set<Invoice> invoices) {
                this.invoices = invoices;
            }

        }

package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.Set;

@Entity
public class Invoice {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int invoiceID;
    private int invoiceNumber;
    private int quantity;

    @ManyToMany
    @JoinTable(
            name = "invoice_product",
            joinColumns = @JoinColumn(name = "invoice_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Invoice() {
    }

    public Invoice(int invoiceNumber, int quantity) {
        this.invoiceNumber = invoiceNumber;
        this.quantity = quantity;
    }

    // Getters and setters
    public int getId() {
        return invoiceID;
    }

    public void setId(int id) {
        this.invoiceID = id;
    }

    public int getInvoiceNumber() {
        return this.invoiceNumber;
    }

    public void setInvoiceNumber(int invoiceNumber) {
        this.invoiceNumber = invoiceNumber;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }
}
![alt text](<HibernateScreenshots/zadanie6 output.png>)

Zadanie 7
aby wykonać zadanie w tym przypadku wystarczy dodać (cascade = CascadeType.ALL) do @ManyToMany.
W celu przetestowania stworzymy nowy produkt Product product4 = new Product("Milk", 40, cat2); i sprawdzimy
czy po utworzeniu nowej Invoice zostanie on dodany do tabeli produktów
        invoice3.getProducts().add(product4);
        entityManager.persist(invoice3);
    
package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.Set;

@Entity
public class Invoice {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int invoiceID;
    private int invoiceNumber;
    private int quantity;

    @ManyToMany(cascade = CascadeType.ALL)
    @JoinTable(
            name = "invoice_product",
            joinColumns = @JoinColumn(name = "invoice_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Invoice() {
    }

    public Invoice(int invoiceNumber, int quantity) {
        this.invoiceNumber = invoiceNumber;
        this.quantity = quantity;
    }

    // Getters and setters
    public int getId() {
        return invoiceID;
    }

    public void setId(int id) {
        this.invoiceID = id;
    }

    public int getInvoiceNumber() {
        return this.invoiceNumber;
    }

    public void setInvoiceNumber(int invoiceNumber) {
        this.invoiceNumber = invoiceNumber;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }
}


        package org.example;

        import jakarta.persistence.*;
        import org.example.Supplier;

        import java.util.HashSet;
        import java.util.List;
        import java.util.Set;

        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.AUTO)
            private int productID;
            public String productName;
            private int unitsInStock;

            @ManyToOne
            @JoinColumn(name = "category_id")
            private Category category;

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Supplier> suppliers = new HashSet<>();

            @ManyToMany(mappedBy = "products", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
            private Set<Invoice> invoices = new HashSet<>();

            public Product() {
            }

            public Product(String productName, int unitsInStock, Category category) {
                this.productName = productName;
                this.unitsInStock = unitsInStock;
                this.category = category;
            }

            public int getId() {
                return productID;
            }

            public void setId(int id) {
                this.productID = id;
            }

            public String getProductName() {
                return productName;
            }

            public void setProductName(String productName) {
                this.productName = productName;
            }
            public Set<Supplier> getSuppliers() {
                return this.suppliers;
            }

            public void setSuppliers(Set<Supplier> suppliers) {
                this.suppliers = suppliers;
            }
            public Set<Invoice> getInvoices() {
                return invoices;
            }

            public void setInvoices(Set<Invoice> invoices) {
                this.invoices = invoices;
            }

        }

![alt text](<HibernateScreenshots/zadanie7 output.png>)

Jak widać, mleko zostało dodane do produktów.

zadanie 8
A)

Dodałem klasę Address embeedable i usunąłem pola związane z adresem z Suppliers zamieniając je na te o nowo 
utworzonej klasie 

package org.example;

import jakarta.persistence.*;

@Embeddable
public class Address {
    private String street;
    private String city;

    public Address() {
    }

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    // Getters and setters
    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }
}

package org.example;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Main {

    private static SessionFactory sessionFactory = null;

    public static void main(String[] args) {
        sessionFactory = getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        Category category1 = new Category("Snacks");
        Category category2 = new Category("Drinks");
        Category category3 = new Category("Sweets");

        Product product1 = new Product("Chips", 20, category1);
        Product product2 = new Product("Energy Drink", 30, category2);
        Product product3 = new Product("Cookies", 40, category3);
        Product product4 = new Product("Juice", 60, category2);
        Product product5 = new Product("Water", 100, category2);
        Product product6 = new Product("Milk", 40, category2);

        Invoice invoice1 = new Invoice(1, 2);
        Invoice invoice2 = new Invoice(2, 3);
        Invoice invoice3 = new Invoice(3, 5);

        invoice3.getProducts().add(product6);
        session.save(invoice3);

        invoice1.getProducts().add(product1);
        invoice1.getProducts().add(product2);

        invoice2.getProducts().add(product2);
        invoice2.getProducts().add(product3);


        product1.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice1);
        product2.getInvoices().add(invoice2);
        product3.getInvoices().add(invoice2);


        Address address1 = new Address("Krakowska", "Kraków");
        Address address2 = new Address("Warszawska", "Warszawa");
        Address address3 = new Address("Olkuska", "Kraków");
        Address address4 = new Address("Warszawska", "Warszawa");
        Address address5 = new Address("Grovestreet", "Los Santos");
        Address address6 = new Address("Kazimierska", "Wrocław");


        Supplier supplier1 = new Supplier("Lays", address1);
        Supplier supplier2 = new Supplier("Crunchips", address2);
        Supplier supplier3 = new Supplier("Monster", address3);
        Supplier supplier4 = new Supplier("Black", address4);
        Supplier supplier5 = new Supplier("Oshee", address5);
        Supplier supplier6 = new Supplier("Oreo", address6);


        product1.getSuppliers().add(supplier1);
        product1.getSuppliers().add(supplier2);
        product1.getSuppliers().add(supplier3);

        product2.getSuppliers().add(supplier3);
        product2.getSuppliers().add(supplier4);
        product2.getSuppliers().add(supplier5);
        product2.getSuppliers().add(supplier1);
        product2.getSuppliers().add(supplier2);

        product3.getSuppliers().add(supplier6);
        product3.getSuppliers().add(supplier2);

        supplier1.getProducts().add(product1);
        supplier1.getProducts().add(product2);

        supplier2.getProducts().add(product3);
        supplier2.getProducts().add(product1);
        supplier2.getProducts().add(product2);

        supplier3.getProducts().add(product2);
        supplier3.getProducts().add(product1);


        supplier4.getProducts().add(product2);
        supplier4.getProducts().add(product3);

        supplier5.getProducts().add(product2);

        supplier6.getProducts().add(product3);


        session.save(category1);
        session.save(category2);
        session.save(category3);

        session.save(product1);
        session.save(product2);
        session.save(product3);
        session.save(product4);
        session.save(product5);

        session.save(supplier1);
        session.save(supplier2);
        session.save(supplier3);
        session.save(supplier4);
        session.save(supplier5);
        session.save(supplier6);

        tx.commit();
        session.close();
    }

    private static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            // Create a configuration instance
            Configuration configuration = new Configuration();

            // Build session factory from the configuration
            sessionFactory = configuration.configure().buildSessionFactory();
        }
        return sessionFactory;
    }
}
![alt text](<HibernateScreenshots/zadanie8a output.png>)

jak widać wszystko dalej działa jak powinno

B)
package org.example;

import jakarta.persistence.*;

@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String street;
    private String city;

    @OneToOne(mappedBy = "address")
    private Supplier supplier;

    public Address() {
    }

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public Supplier getSupplier() {
        return supplier;
    }

    public void setSupplier(Supplier supplier) {
        this.supplier = supplier;
    }
}

package org.example;

import jakarta.persistence.*;
import org.example.Supplier;

import java.util.HashSet;
import java.util.List;
import java.util.Set;
@Entity
@Table(name = "Supplier")
public class Supplier {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id")
    private Address address;

    @ManyToMany
    @JoinTable(
            name = "supplier_product",
            joinColumns = @JoinColumn(name = "supplier_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Supplier() {
    }

    public Supplier(String companyName, Address address) {
        this.companyName = companyName;
        this.address = address;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public void setCompanyName(String companyName) {
        this.companyName = companyName;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public Set<Product> getProducts() {
        return products;
    }

    public void setProducts(Set<Product> products) {
        this.products = products;
    }
}
![alt text](<HibernateScreenshots/zadanie8b Address.png>)
![alt text](<HibernateScreenshots/zadanie8b Supplier.png>)

zadanie 9

package org.example;

import jakarta.persistence.*;

@Entity
@Inheritance(strategy = InheritanceType.JOINED) // Zmieniamy strategię w zależności od potrzeb
public abstract class Company {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String companyName;
    private String street;
    private String city;
    private String zipCode;

    public Company() {}

    public Company(String companyName, String street, String city, String zipCode) {
        this.companyName = companyName;
        this.street = street;
        this.city = city;
        this.zipCode = zipCode;
    }

    // Getters and setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCompanyName() {
        return companyName;
    }

    public void setCompanyName(String companyName) {
        this.companyName = companyName;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getZipCode() {
        return zipCode;
    }

    public void setZipCode(String zipCode) {
        this.zipCode = zipCode;
    }
}


package org.example;

import jakarta.persistence.*;

@Entity
public class Customer extends Company {
    private double discount;

    public Customer() {}

    public Customer(String companyName, String street, String city, String zipCode, double discount) {
        super(companyName, street, city, zipCode);
        this.discount = discount;
    }

    // Getters and setters
    public double getDiscount() {
        return discount;
    }

    public void setDiscount(double discount) {
        this.discount = discount;
    }
}

package org.example;

import jakarta.persistence.*;

import java.util.HashSet;
import java.util.Set;

@Entity
public class Supplier extends Company {
    private String bankAccountNumber;

    public Supplier() {}
    @ManyToMany
    @JoinTable(
            name = "supplier_product",
            joinColumns = @JoinColumn(name = "supplier_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products = new HashSet<>();

    public Supplier(String companyName, String street, String city, String zipCode, String bankAccountNumber) {
        super(companyName, street, city, zipCode);
        this.bankAccountNumber = bankAccountNumber;
    }

    // Getters and setters
    public String getBankAccountNumber() {
        return bankAccountNumber;
    }

    public void setBankAccountNumber(String bankAccountNumber) {
        this.bankAccountNumber = bankAccountNumber;
    }
}



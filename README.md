using System;
using System.Data;
using System.Data.SqlClient;

class Inventory
{
    static string connectionString =
    @"Data Source=.;Initial Catalog=InventoryDB;Integrated Security=True;TrustServerCertificate=True";

    static void Main()
    {
        while (true)
        {
            Console.WriteLine("\n--- INVENTORY MANAGEMENT ---");
            Console.WriteLine("1. Add Product");
            Console.WriteLine("2. View Products");
            Console.WriteLine("3. Update Product");
            Console.WriteLine("4. Delete Product");
            Console.WriteLine("5. Exit");

            Console.Write("Enter Choice: ");
            int choice = Convert.ToInt32(Console.ReadLine());

            switch (choice)
            {
                case 1: AddProduct(); break;
                case 2: ViewProducts(); break;
                case 3: UpdateProduct(); break;
                case 4: DeleteProduct(); break;
                case 5: return;
                default: Console.WriteLine("Invalid Choice"); break;
            }
        }
    }

    // CREATE
    static void AddProduct()
    {
        using (SqlConnection con = new SqlConnection(connectionString))
        {
            string query = @"INSERT INTO Products
            (ProductName, Category, Quantity, Price)
            VALUES (@name, @category, @quantity, @price)";

            SqlCommand cmd = new SqlCommand(query, con);

            Console.Write("Product Name: ");
            cmd.Parameters.AddWithValue("@name", Console.ReadLine());

            Console.Write("Category: ");
            cmd.Parameters.AddWithValue("@category", Console.ReadLine());

            Console.Write("Quantity: ");
            cmd.Parameters.AddWithValue("@quantity",
                Convert.ToInt32(Console.ReadLine()));

            Console.Write("Price: ");
            cmd.Parameters.AddWithValue("@price",
                Convert.ToDecimal(Console.ReadLine()));

            con.Open();
            cmd.ExecuteNonQuery();

            Console.WriteLine("Product Added Successfully!");
        }
    }

    // READ
    static void ViewProducts()
    {
        using (SqlConnection con = new SqlConnection(connectionString))
        {
            string query = "SELECT * FROM Products";

            SqlDataAdapter da = new SqlDataAdapter(query, con);
            DataTable dt = new DataTable();

            da.Fill(dt);

            foreach (DataRow row in dt.Rows)
            {
                Console.WriteLine(
                    $"{row["ProductID"]} | " +
                    $"{row["ProductName"]} | " +
                    $"{row["Category"]} | " +
                    $"{row["Quantity"]} | " +
                    $"{row["Price"]}");
            }
        }
    }

    // UPDATE
    static void UpdateProduct()
    {
        using (SqlConnection con = new SqlConnection(connectionString))
        {
            string query = @"UPDATE Products
            SET ProductName=@name, Category=@category,
            Quantity=@quantity, Price=@price
            WHERE ProductID=@id";

            SqlCommand cmd = new SqlCommand(query, con);

            Console.Write("Product ID: ");
            cmd.Parameters.AddWithValue("@id",
                Convert.ToInt32(Console.ReadLine()));

            Console.Write("New Product Name: ");
            cmd.Parameters.AddWithValue("@name", Console.ReadLine());

            Console.Write("New Category: ");
            cmd.Parameters.AddWithValue("@category", Console.ReadLine());

            Console.Write("New Quantity: ");
            cmd.Parameters.AddWithValue("@quantity",
                Convert.ToInt32(Console.ReadLine()));

            Console.Write("New Price: ");
            cmd.Parameters.AddWithValue("@price",
                Convert.ToDecimal(Console.ReadLine()));

            con.Open();

            int result = cmd.ExecuteNonQuery();

            Console.WriteLine(result > 0 ?
                "Product Updated Successfully!" : "Product Not Found!");
        }
    }

    // DELETE
    static void DeleteProduct()
    {
        using (SqlConnection con = new SqlConnection(connectionString))
        {
            string query =
                "DELETE FROM Products WHERE ProductID=@id";

            SqlCommand cmd = new SqlCommand(query, con);

            Console.Write("Enter Product ID: ");
            cmd.Parameters.AddWithValue("@id",
                Convert.ToInt32(Console.ReadLine()));

            con.Open();

            int result = cmd.ExecuteNonQuery();

            Console.WriteLine(result > 0 ?
                "Product Deleted Successfully!"

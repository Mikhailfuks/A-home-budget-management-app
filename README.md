using System;
using System.Collections.Generic;

namespace HomeBudgetApp
{
    // Класс для представления транзакции
    public class Transaction
    {
        public int Id { get; set; }
        public DateTime Date { get; set; }
        public string Description { get; set; }
        public decimal Amount { get; set; }
        public TransactionType Type { get; set; }
        public Category Category { get; set; }

        // Конструктор
        public Transaction(int id, DateTime date, string description, decimal amount, 
                           TransactionType type, Category category)
        {
            Id = id;
            Date = date;
            Description = description;
            Amount = amount;
            Type = type;
            Category = category;
        }
    }

    // Перечисление для типа транзакции (доход или расход)
    public enum TransactionType
    {
        Income,
        Expense
    }

    // Класс для представления категории транзакции
    public class Category
    {
        public int Id { get; set; }
        public string Name { get; set; }

        // Конструктор
        public Category(int id, string name)
        {
            Id = id;
            Name = name;
        }
    }

    // Класс для управления бюджетом
    public class BudgetManager
    {
        private List<Transaction> transactions = new List<Transaction>();
        private int nextTransactionId = 1;
        private List<Category> categories = new List<Category>();
        private decimal currentBalance = 0;

        // Добавление категории
        public void AddCategory(string name)
        {
            categories.Add(new Category(categories.Count + 1, name));
        }

        // Добавление транзакции
        public void AddTransaction(DateTime date, string description, decimal amount, 
                                  TransactionType type, string categoryName)
        {
            Category category = categories.Find(c => c.Name == categoryName);

            if (category == null)
            {
                AddCategory(categoryName);
                category = categories.Find(c => c.Name == categoryName);
            }

            Transaction transaction = new Transaction(nextTransactionId++, date, description, amount, type, category);
            transactions.Add(transaction);

            if (type == TransactionType.Income)
            {
                currentBalance += amount;
            }
            else
            {
                currentBalance -= amount;
            }
        }

        // Получение списка транзакций
        public List<Transaction> GetTransactions()
        {
            return transactions;
        }

        // Получение списка категорий
        public List<Category> GetCategories()
        {
            return categories;
        }

        // Получение текущего баланса
        public decimal GetCurrentBalance()
        {
            return currentBalance;
        }

        // Получение суммарных расходов по категории
        public decimal GetTotalExpensesByCategory(string categoryName)
        {
            return transactions.Where(t => t.Type == TransactionType.Expense && t.Category.Name == categoryName)
                               .Sum(t => t.Amount);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // Создание менеджера бюджета
            BudgetManager budgetManager = new BudgetManager();

            // Добавление категорий
            budgetManager.AddCategory("Еда");
            budgetManager.AddCategory("Транспорт");
            budgetManager.AddCategory("Развлечения");

            // Добавление транзакций
            budgetManager.AddTransaction(DateTime.Now.AddDays(-1), "Зарплата", 5000, TransactionType.Income, "Доходы");
            budgetManager.AddTransaction(DateTime.Now.AddDays(-2), "Продукты", 500, TransactionType.Expense, "Еда");
            budgetManager.AddTransaction(DateTime.Now.AddDays(-3), "Бензин", 100, TransactionType.Expense, "Транспорт");
            budgetManager.AddTransaction(DateTime.Now.AddDays(-4), "Билеты в кино", 200, TransactionType.Expense, "Развлечения");

            // Вывод списка транзакций
            Console.WriteLine("Список транзакций:");
            foreach (Transaction transaction in budgetManager.GetTransactions())
            {
                Console.WriteLine($"{transaction.Id}. {transaction.Date:dd.MM.yyyy} - {transaction.Description} ({transaction.Type}) - {transaction.Amount:C} ({transaction.Category.Name})");
            }

            // Вывод текущего баланса
            Console.WriteLine($"\nТекущий баланс: {budgetManager.GetCurrentBalance():C}");

            // Вывод суммарных расходов по категории
            Console.WriteLine($"\nСуммарные расходы на 'Еду': {budgetManager.GetTotalExpensesByCategory("Еда"):C}");

            Console.ReadKey();
        }
    }
}

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>SAKI MANAGEMENT SYSTEM</title>
<style>
  body {
    font-family: Arial, sans-serif;
    max-width: 700px;
    margin: 80px auto 20px;
    background: #f9f9f9;
    padding: 10px;
  }
  h1, h2 {
    text-align: center;
    color: #333;
  }
  .section {
    background: white;
    padding: 15px 20px;
    margin-bottom: 25px;
    border-radius: 8px;
    box-shadow: 0 0 8px rgba(0,0,0,0.1);
  }
  input, button, select {
    padding: 8px;
    margin: 5px 0;
    font-size: 1rem;
    width: 100%;
    box-sizing: border-box;
    border-radius: 4px;
    border: 1px solid #ccc;
  }
  button {
    background-color: #007bff;
    color: white;
    border: none;
    cursor: pointer;
    font-weight: bold;
  }
  button:hover {
    background-color: #0056b3;
  }
  ul {
    list-style: none;
    padding-left: 0;
    max-height: 150px;
    overflow-y: auto;
    border-top: 1px solid #ddd;
    margin-top: 10px;
  }
  li {
    background: #f1f1f1;
    margin: 8px 0;
    padding: 8px 12px;
    border-radius: 4px;
    display: flex;
    justify-content: space-between;
  }
  .summary {
    font-weight: bold;
    font-size: 1.2em;
    text-align: center;
    margin-top: 10px;
    color: #222;
  }
  label {
    font-weight: 600;
  }
  /* Fixed top summary bar */
  #topSummary {
    position: fixed;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 700px;
    background: #007bff;
    color: white;
    padding: 12px 20px;
    font-weight: bold;
    font-size: 1.1em;
    display: flex;
    justify-content: space-around;
    border-radius: 0 0 10px 10px;
    box-shadow: 0 3px 10px rgba(0,0,0,0.3);
    z-index: 1000;
  }
  #topSummary div {
    flex: 1;
    text-align: center;
  }
</style>
</head>
<body>

<div id="topSummary">
  <div>Total Balance: <span id="topBalance">0.00</span></div>
  <div>Total Income: <span id="topIncome">0.00</span></div>
  <div>Total Expense: <span id="topExpense">0.00</span></div>
</div>

<h1>SAKI MANAGEMENT SYSTEM</h1>

<div class="section">
  <h2>Customer Management</h2>
  <input type="text" id="customerName" placeholder="Customer Name" />
  <button onclick="addCustomer()">Add Customer</button>
  <ul id="customerList"></ul>
</div>

<div class="section">
  <h2>Purchase Entry</h2>
  <input type="text" id="purchaseItem" placeholder="Purchase Item Description" />
  <input type="number" id="purchaseAmount" placeholder="Purchase Amount" />
  <button onclick="addPurchase()">Add Purchase</button>
  <ul id="purchaseList"></ul>
</div>

<div class="section">
  <h2>Daily Sales Entry</h2>
  <label for="saleCustomer">Select Customer</label>
  <select id="saleCustomer">
    <option value="">--Select Customer--</option>
  </select>
  <input type="number" id="saleCashAmount" placeholder="Cash Sale Amount" />
  <input type="number" id="saleCreditAmount" placeholder="Credit Sale Amount" />
  <button onclick="addSale()">Add Sale</button>
  <ul id="salesList"></ul>
</div>

<div class="section">
  <h2>Expense Entry</h2>
  <input type="text" id="expenseDescription" placeholder="Expense Description" />
  <input type="number" id="expenseAmount" placeholder="Expense Amount" />
  <button onclick="addExpense()">Add Expense</button>
  <ul id="expenseList"></ul>
</div>

<div class="section summary">
  <div>Total Income (Cash + Credit): <span id="totalIncome">0</span></div>
  <div>Total Expenses (Purchases + Expenses): <span id="totalExpense">0</span></div>
  <div>Current Balance: <span id="balance">0</span></div>
</div>

<script>
  let customers = [];
  let purchases = [];
  let sales = [];
  let expenses = [];

  // Add customer
  function addCustomer() {
    const nameInput = document.getElementById('customerName');
    const name = nameInput.value.trim();
    if (!name) {
      alert('Please enter a customer name');
      return;
    }
    customers.push(name);
    nameInput.value = '';
    renderCustomers();
    populateCustomerDropdown();
  }

  function renderCustomers() {
    const ul = document.getElementById('customerList');
    ul.innerHTML = '';
    customers.forEach((cust, i) => {
      const li = document.createElement('li');
      li.textContent = cust;
      ul.appendChild(li);
    });
  }

  // Populate customer dropdown for sales
  function populateCustomerDropdown() {
    const select = document.getElementById('saleCustomer');
    select.innerHTML = '<option value="">--Select Customer--</option>';
    customers.forEach(cust => {
      const option = document.createElement('option');
      option.value = cust;
      option.textContent = cust;
      select.appendChild(option);
    });
  }

  // Add purchase
  function addPurchase() {
    const desc = document.getElementById('purchaseItem').value.trim();
    const amount = parseFloat(document.getElementById('purchaseAmount').value);
    if (!desc || isNaN(amount) || amount <= 0) {
      alert('Please enter valid purchase details');
      return;
    }
    purchases.push({desc, amount});
    document.getElementById('purchaseItem').value = '';
    document.getElementById('purchaseAmount').value = '';
    renderPurchases();
    updateSummary();
  }

  function renderPurchases() {
    const ul = document.getElementById('purchaseList');
    ul.innerHTML = '';
    purchases.forEach(p => {
      const li = document.createElement('li');
      li.textContent = `${p.desc} : ${p.amount.toFixed(2)}`;
      ul.appendChild(li);
    });
  }

  // Add sale
  function addSale() {
    const customer = document.getElementById('saleCustomer').value;
    const cashAmount = parseFloat(document.getElementById('saleCashAmount').value) || 0;
    const creditAmount = parseFloat(document.getElementById('saleCreditAmount').value) || 0;

    if (!customer) {
      alert('Please select a customer');
      return;
    }
    if (cashAmount <= 0 && creditAmount <= 0) {
      alert('Please enter cash or credit sale amount');
      return;
    }

    sales.push({customer, cashAmount, creditAmount});
    document.getElementById('saleCustomer').value = '';
    document.getElementById('saleCashAmount').value = '';
    document.getElementById('saleCreditAmount').value = '';
    renderSales();
    updateSummary();
  }

  function renderSales() {
    const ul = document.getElementById('salesList');
    ul.innerHTML = '';
    sales.forEach(sale => {
      const li = document.createElement('li');
      li.textContent = `${sale.customer} - Cash: ${sale.cashAmount.toFixed(2)} | Credit: ${sale.creditAmount.toFixed(2)}`;
      ul.appendChild(li);
    });
  }

  // Add expense
  function addExpense() {
    const desc = document.getElementById('expenseDescription').value.trim();
    const amount = parseFloat(document.getElementById('expenseAmount').value);
    if (!desc || isNaN(amount) || amount <= 0) {
      alert('Please enter valid expense details');
      return;
    }
    expenses.push({desc, amount});
    document.getElementById('expenseDescription').value = '';
    document.getElementById('expenseAmount').value = '';
    renderExpenses();
    updateSummary();
  }

  function renderExpenses() {
    const ul = document.getElementById('expenseList');
    ul.innerHTML = '';
    expenses.forEach(exp => {
      const li = document.createElement('li');
      li.textContent = `${exp.desc} : ${exp.amount.toFixed(2)}`;
      ul.appendChild(li);
    });
  }

  // Update summary totals
  function updateSummary() {
    const totalCashSales = sales.reduce((sum, s) => sum + s.cashAmount, 0);
    const totalCreditSales = sales.reduce((sum, s) => sum + s.creditAmount, 0);
    const totalIncome = totalCashSales + totalCreditSales;

    const totalPurchases = purchases.reduce((sum, p) => sum + p.amount, 0);
    const totalExpenses = expenses.reduce((sum, e) => sum + e.amount, 0);
    const totalOutgoings = totalPurchases + totalExpenses;

    const balance = totalIncome - totalOutgoings;

    document.getElementById('totalIncome').textContent = totalIncome.toFixed(2);
    document.getElementById('totalExpense').textContent = totalOutgoings.toFixed(2);
    document.getElementById('balance').textContent = balance.toFixed(2);

    // Update top fixed summary
    document.getElementById('topBalance').textContent = balance.toFixed(2);
    document.getElementById('topIncome').textContent = totalIncome.toFixed(2);
    document.getElementById('topExpense').textContent = totalOutgoings.toFixed(2);
  }

  // Initialize empty lists
  renderCustomers();
  renderPurchases();
  renderSales();
  renderExpenses();
  updateSummary();
</script>

</body>
</html>

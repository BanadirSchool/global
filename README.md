<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Global General Trading Company</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; padding: 20px; background-color: #1a1a2e; color: white; }
        header { display: flex; align-items: center; justify-content: space-between; background-color: #660000; padding: 10px; }
        h1 { color: #003366; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; background-color: #222; color: white; border-radius: 10px; overflow: hidden; }
        th, td { border: 2px solid #ff4500; padding: 12px; text-align: left; }
        th { background-color: #003366; color: white; }
        td { background-color: #1a1a2e; }
        .actions button { margin-right: 5px; }
        #totalSum { font-weight: bold; margin-top: 10px; }
        @media print {
            form, #searchBox, button { display: none; }
        }
    </style>
</head>
<body>
    <header>
        <h1>Global General Trading Company</h1>
        <img src="logo.png" alt="Company Logo" width="100">
    </header>
    
    <form id="itemForm">
        <input type="text" id="itemName" placeholder="Item Name" required>
        <input type="number" id="quantity" placeholder="Quantity" required>
        <input type="number" id="price" placeholder="Price per Unit" min="0" step="any">
        <button type="submit">Add Item</button>
    </form>
    
    <input type="text" id="searchBox" placeholder="Search Items...">
    
    <table id="inventoryTable">
        <thead>
            <tr>
                <th>No.</th>
                <th>Item Name</th>
                <th>Quantity</th>
                <th>Price per Unit</th>
                <th>Total</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody></tbody>
    </table>
    
    <p id="totalSum">Total Sum: $0</p>
    
    <button onclick="printTable()">Print</button>
    <select id="whatsappOption">
        <option value="zoobe">Zoobe</option>
        <option value="bakaaro">Bakaaro</option>
    </select>
    <button onclick="sendToWhatsApp()">Send to WhatsApp</button>
    <button onclick="undoDelete()">Undo</button>
    <script>
        function sendToWhatsApp() {
            let message = "Inventory List:\n";
            inventory.forEach((item, index) => {
                message += `${index + 1}. ${item.name} - ${item.quantity} x $${item.price} = $${item.quantity * item.price}\n`;
            });
            let selectedOption = document.getElementById("whatsappOption").value;
            let whatsappURL = selectedOption === "zoobe" 
                ? `https://wa.me/message/JDAWM4YHJCUSD1${encodeURIComponent(message)}`
                : `https://wa.me/qr/7ZBGWBO4HXMFJ1${encodeURIComponent(message)}`;
            
            window.open(whatsappURL, "_blank");
        }
    </script>
    
    
    <script>
        let inventory = JSON.parse(localStorage.getItem("inventory")) || [];
        let deletedItem = null;
        
        function renderTable() {
            const tableBody = document.querySelector("#inventoryTable tbody");
            tableBody.innerHTML = "";
            let totalSum = 0;
            inventory.forEach((item, index) => {
                let total = item.quantity * item.price;
                totalSum += total;
                tableBody.innerHTML += `
                    <tr>
                        <td>${index + 1}</td>
                        <td contenteditable="true" onblur="editItem(${index}, 'name', this.innerText)">${item.name}</td>
                        <td contenteditable="true" onblur="editItem(${index}, 'quantity', this.innerText)">${item.quantity}</td>
                        <td contenteditable="true" onblur="editItem(${index}, 'price', this.innerText)">${item.price}</td>
                        <td>$${total}</td>
                        <td class="actions">
                            <button onclick="deleteItem(${index})">Delete</button>
                        </td>
                    </tr>
                `;
            });
            document.getElementById("totalSum").innerText = `Total Sum: $${totalSum}`;
            localStorage.setItem("inventory", JSON.stringify(inventory));
        }
        
        function addItem(event) {
            event.preventDefault();
            const name = document.getElementById("itemName").value;
            const quantity = Number(document.getElementById("quantity").value);
            const price = document.getElementById("price").value ? Number(document.getElementById("price").value) : 0;
            inventory.push({ name, quantity, price });
            document.getElementById("itemForm").reset();
            renderTable();
        }
        
        function editItem(index, field, value) {
            inventory[index][field] = field === 'quantity' || field === 'price' ? Number(value) : value;
            renderTable();
        }
        
        function deleteItem(index) {
            deletedItem = inventory[index];
            inventory.splice(index, 1);
            renderTable();
        }
        
        function undoDelete() {
            if (deletedItem) {
                inventory.push(deletedItem);
                deletedItem = null;
                renderTable();
            }
        }
        
        function printTable() {
            window.print();
        }
        
        function sendToWhatsApp() {
            let message = "Inventory List:\n";
            inventory.forEach((item, index) => {
                message += `${index + 1}. ${item.name} - ${item.quantity} x $${item.price} = $${item.quantity * item.price}\n`;
            });
            let selectedOption = document.getElementById("whatsappOption").value;
            let whatsappURL = selectedOption === "zoobe" 
                ? `https://wa.me/qr/7ZBGWBO4HXMFJ1?text=${encodeURIComponent(message)}`
                : `https://wa.me/message/JDAWM4YHJCUSD1?text=${encodeURIComponent(message)}`;
            
            window.open(whatsappURL, "_blank");
        }
        
        document.getElementById("itemForm").addEventListener("submit", addItem);
        renderTable();
    </script>
</body>
</html>

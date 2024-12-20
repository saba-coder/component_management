# test
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Check-In/Check-Out System</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        background-color: #f4f4f4;
        margin: 0;
        padding: 0;
      }
      .header {
        background-color: #343a40;
        color: white;
        padding: 10px;
        text-align: left;
        font-size: 24px;
      }
      .container {
        padding: 20px;
      }
      .tabs {
        display: flex;
        background-color: #ddd;
        margin-top: 10px;
      }
      .tab {
        padding: 10px 20px;
        cursor: pointer;
        background-color: #ccc;
        border: 1px solid #bbb;
        margin-right: 5px;
      }
      .tab.active {
        background-color: #fff;
        border-bottom: none;
      }
      table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
      }
      table,
      th,
      td {
        border: 1px solid #ccc;
      }
      th,
      td {
        padding: 10px;
        text-align: center;
      }
      input[type="text"],
      input[type="date"] {
        width: 100%;
        padding: 5px;
        margin: 5px 0;
        border: 1px solid #ddd;
        border-radius: 5px;
      }
      .checkin,
      .checkout,
      .submit-reservation {
        background-color: #28a745;
        color: white;
        border: none;
        border-radius: 5px;
        padding: 8px 12px;
        cursor: pointer;
      }
      .checkout {
        background-color: #dc3545;
      }
    </style>
  </head>
  <body>
    <div id="login-section">
      <div class="header">Emerson Login Page</div>
      <div class="login-container">
        <input
          type="text"
          id="username"
          placeholder="Enter Username"
          required
        />
        <input
          type="password"
          id="password"
          placeholder="Enter Password"
          required
        />
        <button class="login-btn" onclick="login()">Login</button>
        <div id="login-status" style="color: red; margin-top: 10px"></div>
      </div>
    </div>

    <div id="product-info-section" style="display: none">
      <div class="header">Product Information</div>
      <div class="container">
        <div class="tabs">
          <div class="tab active" onclick="showTab('components')">
            Components
          </div>
          <div class="tab" onclick="showTab('systems')">Systems</div>
          <div class="tab" onclick="showTab('upcoming')">Upcoming</div>
        </div>

        <div id="components" class="tab-content" style="display: block">
          <h2>Components</h2>
          <input
            type="text"
            id="search-input"
            placeholder="Search by Serial Number, Product Info, or Type"
            oninput="filterTable()"
          />
          <table id="components-table">
            <thead>
              <tr>
                <th>Serial Number</th>
                <th>Product Info</th>
                <th>Data Sheet</th>
                <th>Type</th>
                <th>Status</th>
                <th>Current User</th>
                <th>Check-In/Out Dates</th>
                <th>Last User Info</th>
                <th>Future Reservations</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody></tbody>
          </table>
        </div>

        <div id="systems" class="tab-content" style="display: none">
          <h2>Systems</h2>
          <p>No systems available currently.</p>
        </div>

        <div id="upcoming" class="tab-content" style="display: none">
          <h2>Upcoming Products</h2>
          <p>No upcoming products listed.</p>
        </div>
      </div>
    </div>

    <script>
      let currentUser = "";
      const users = [
        { username: "user1", password: "password1" },
        { username: "user2", password: "password2" },
        { username: "user3", password: "password3" },
      ];

      const components = [
        {
          serial: "C001",
          info: "Laptop",
          dataSheet: "#",
          type: "Electronics",
          status: "Available",
          currentUser: "N/A",
          lastUser: "N/A",
          dates: "N/A",
          reservations: [],
        },
        {
          serial: "C002",
          info: "Projector",
          dataSheet: "#",
          type: "Electronics",
          status: "Available",
          currentUser: "N/A",
          lastUser: "N/A",
          dates: "N/A",
          reservations: [],
        },
      ];

      function login() {
        const username = document.getElementById("username").value;
        const password = document.getElementById("password").value;
        const user = users.find(
          (u) => u.username === username && u.password === password
        );

        if (user) {
          currentUser = user.username;
          document.getElementById("login-status").innerText = "";
          document.getElementById("login-section").style.display = "none";
          document.getElementById("product-info-section").style.display =
            "block";
          populateTable(components);
        } else {
          document.getElementById("login-status").innerText =
            "Invalid username or password!";
        }
      }

      function populateTable(items) {
        const tableBody = document.querySelector(`#components-table tbody`);
        tableBody.innerHTML = "";

        items.forEach((item) => {
          const row = document.createElement("tr");
          row.innerHTML = `
                <td>${item.serial}</td>
                <td>${item.info}</td>
                <td><a href="${item.dataSheet}" target="_blank">Open</a></td>
                <td>${item.type}</td>
                <td>${item.status}</td>
                <td>${item.currentUser}</td>
                <td>${item.dates}</td>
                <td>${item.lastUser}</td>
                <td>
                    <input type="text" placeholder="User Name" id="resUser-${
                      item.serial
                    }">
                    <input type="date" id="resDate-${item.serial}">
                    <button class="submit-reservation" onclick="submitReservation('${
                      item.serial
                    }')">Submit</button>
                    <div id="reservations-${item.serial}">
                        ${formatReservations(item.reservations)}
                    </div>
                </td>
                <td>
                    <button class="checkin" onclick="checkIn('${
                      item.serial
                    }')">Check In</button>
                    <button class="checkout" onclick="checkOut('${
                      item.serial
                    }')">Check Out</button>
                </td>
            `;
          tableBody.appendChild(row);
        });
      }

      function formatReservations(reservations) {
        return reservations.map((r) => `<p>${r.user} - ${r.date}</p>`).join("");
      }

      function submitReservation(serial) {
        const user = document.getElementById(`resUser-${serial}`).value;
        const date = document.getElementById(`resDate-${serial}`).value;

        if (user && date) {
          const item = components.find((c) => c.serial === serial);
          item.reservations.push({ user, date });
          populateTable(components);
        } else {
          alert("Please fill both name and date for the reservation.");
        }
      }

      function checkIn(serial) {
        const item = components.find((c) => c.serial === serial);
        if (item.status === "Checked In") return alert("Already checked in!");

        item.status = "Checked In";
        item.currentUser = currentUser;
        item.dates = new Date().toLocaleString();
        item.lastUser = currentUser;
        populateTable(components);
      }

      function checkOut(serial) {
        const item = components.find((c) => c.serial === serial);
        if (item.status === "Available") return alert("Not checked in!");

        item.status = "Available";
        item.currentUser = "N/A";
        item.dates += ` | Checked Out: ${new Date().toLocaleString()}`;
        populateTable(components);
      }

      function showTab(tabName) {
        document
          .querySelectorAll(".tab-content")
          .forEach((content) => (content.style.display = "none"));
        document.getElementById(tabName).style.display = "block";
        document
          .querySelectorAll(".tab")
          .forEach((tab) => tab.classList.remove("active"));
        event.target.classList.add("active");
      }
    </script>
  </body>
</html>

<!DOCTYPE html>
<html lang="my">
<head>
    <meta charset="UTF-8">
    <title>HR Admin - Employee Registration</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f4f7f6; padding: 20px; }
        .form-container { max-width: 600px; background: white; padding: 30px; border-radius: 8px; margin: auto; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        input, select, textarea { width: 100%; padding: 8px; box-sizing: border-box; border: 1px solid #ccc; border-radius: 4px; }
        .error { color: red; font-size: 12px; display: none; }
        button { background: #007bff; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; width: 100%; font-size: 16px; }
        button:hover { background: #0056b3; }
    </style>
</head>
<body>

<div class="form-container">
    <h2>ဝန်ထမ်းအသစ် မှတ်ပုံတင်ရန် Form</h2>
    <form id="regForm" onsubmit="submitForm(event)">
        
        <div class="form-group">
            <label>Employee ID (ဂဏန်း ၆ လုံးတိတိ)</label>
            <input type="text" id="employeeId" maxlength="6" pattern="[0-9]{6}" placeholder="ဥပမာ- 123456" required oninput="this.value = this.value.replace(/[^0-9]/g, '')">
            <small class="error" id="errId">ဂဏန်း (၆) လုံးသာ ရိုက်ထည့်ပါ။</small>
        </div>

        <div class="form-group">
            <label>Full Name (English စာသားသီးသန့်)</label>
            <input type="text" id="fullName" pattern="[A-Za-z\s]+" placeholder="U Aung Aung" required oninput="this.value = this.value.replace(/[^A-Za-z\s]/g, '')">
        </div>

        <div class="form-group">
            <label>Father's Name (English စာသားသီးသန့်)</label>
            <input type="text" id="fatherName" pattern="[A-Za-z\s]+" placeholder="U Ba" required oninput="this.value = this.value.replace(/[^A-Za-z\s]/g, '')">
        </div>

        <div class="form-group">
            <label>Date of Birth (ရက်၊ လ၊ နှစ်)</label>
            <input type="date" id="dob" required onchange="calculateAge()">
        </div>

        <div class="form-group">
            <label>Age (အသက် - အလိုအလျောက်ပေါ်မည်)</label>
            <input type="text" id="age" readonly style="background: #e9ecef;">
        </div>

        <div class="form-group">
            <label>Address</label>
            <textarea id="address" rows="2" required></textarea>
        </div>

        <div class="form-group">
            <label>NRC Number</label>
            <input type="text" id="nrcNumber" placeholder="12/MaGaTa(N)123456" required>
        </div>

        <div class="form-group">
            <label>Role</label>
            <select id="role" required>
                <option value="">ရာထူး ရွေးချယ်ပါ</option>
                <option value="leader">Leader</option>
                <option value="second leader">Second Leader</option>
                <option value="helper">Helper</option>
            </select>
        </div>

        <div class="form-group">
            <label>Phone Number (09- ဖြင့်စပြီး ဂဏန်း ၉ လုံး)</label>
            <input type="text" id="phoneNumber" value="09-" maxlength="11" required oninput="validatePhone(this)">
        </div>

        <div class="form-group">
            <label>Joining Date (အလုပ်စတင်ဝင်သည့်ရက် - ယနေ့)</label>
            <input type="date" id="joiningDate" readonly style="background: #e9ecef;">
        </div>

        <div class="form-group">
            <label>Employee Photo (ယူနီဖောင်းဝတ်ဆင်ထားသော ပုံ)</label>
            <input type="file" id="photoFile" accept="image/*" required>
        </div>

        <div class="form-group">
            <label>NRC Front Photo</label>
            <input type="file" id="nrcFrontFile" accept="image/*" required>
        </div>

        <div class="form-group">
            <label>NRC Back Photo</label>
            <input type="file" id="nrcBackFile" accept="image/*" required>
        </div>

        <div class="form-group">
            <label>Remarks</label>
            <textarea id="remarks" rows="2"></textarea>
        </div>

        <button type="submit">ဒေတာ သိမ်းဆည်းမည်</button>
    </form>
</div>

<script>
    // နေ့စွဲအလိုအလျောက် ဖြည့်ရန် (Joining Date = Today)
    document.getElementById('joiningDate').valueAsDate = new Date();

    // အသက် အလိုအလျောက် တွက်ချက်ခြင်း
    function calculateAge() {
        const dobVal = document.getElementById('dob').value;
        if (!dobVal) return;
        const dobDate = new Date(dobVal);
        const diff = Date.now() - dobDate.getTime();
        const ageDate = new Date(diff);
        const age = Math.abs(ageDate.getUTCFullYear() - 1970);
        document.getElementById('age').value = age + " နှစ်";
    }

    // ဖုန်းနံပါတ် စစ်ဆေးခြင်း (09- ဖြင့်စရန် နှင့် ဂဏန်း ၉ လုံးတိတိဖြစ်ရန်)
    function validatePhone(input) {
        if (!input.value.startsWith("09-")) {
            input.value = "09-";
        }
        let digits = input.value.substring(3).replace(/[^0-9]/g, '');
        if (digits.length > 9) {
            digits = digits.substring(0, 9);
        }
        input.value = "09-" + digits;
    }

    // Base64 သို့ ပြောင်းလဲသည့် Helper Function
    function getBase64(file) {
        return new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => resolve(reader.result);
            reader.onerror = error => reject(error);
        });
    }

    async function submitForm(event) {
        event.preventDefault();
        
        const phone = document.getElementById('phoneNumber').value;
        if (phone.length !== 12) { // 09- (3 chars) + 9 digits = 12 chars
            alert("ဖုန်းနံပါတ် တိုနေပါသည် သို့မဟုတ် မှားယွင်းနေပါသည်။ (၉ လုံးတိတိ ဖြစ်ရမည်)");
            return;
        }

        const photoFile = document.getElementById('photoFile').files[0];
        const nrcFrontFile = document.getElementById('nrcFrontFile').files[0];
        const nrcBackFile = document.getElementById('nrcBackFile').files[0];

        const formData = {
            employeeId: document.getElementById('employeeId').value,
            fullName: document.getElementById('fullName').value,
            fatherName: document.getElementById('fatherName').value,
            dob: document.getElementById('dob').value,
            age: document.getElementById('age').value,
            address: document.getElementById('address').value,
            nrcNumber: document.getElementById('nrcNumber').value,
            role: document.getElementById('role').value,
            phoneNumber: phone,
            joiningDate: document.getElementById('joiningDate').value,
            photoBase64: await getBase64(photoFile),
            nrcFrontBase64: await getBase64(nrcFrontFile),
            nrcBackBase64: await getBase64(nrcBackFile),
            remarks: document.getElementById('remarks').value
        };

        const SCRIPT_URL = "YOUR_APPS_SCRIPT_WEB_APP_URL_HERE"; // Apps Script Deploy URL ထည့်ရန်

        alert("ဒေတာများကို ပို့ဆောင်နေပါပြီ...");

        fetch(SCRIPT_URL, {
            method: 'POST',
            body: JSON.stringify(formData)
        })
        .then(response => response.json())
        .then(result => {
            alert(result.message);
            if (result.status === "success") {
                document.getElementById('regForm').reset();
                document.getElementById('joiningDate').valueAsDate = new Date();
                document.getElementById('phoneNumber').value = "09-";
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert("ချိတ်ဆက်မှု အမှားအယွင်း ရှိနေပါသည်။");
        });
    }
</script>

</body>
</html>

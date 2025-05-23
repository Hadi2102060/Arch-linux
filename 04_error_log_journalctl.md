<br>
<br>

# `#1: See Error Log: (With Journalctl)  `

<br>
<br>

# `#1.1 See the error log: `

<br>
<br>

Arch Linux-এ শুধুমাত্র error log দেখতে চাইলে, `journalctl` কমান্ড ব্যবহার করতে পারেন:
```bash
journalctl -p err
```
- `-p` ফ্ল্যাগটি **priority** নির্দেশ করে।
- `err` হলো **error** এর জন্য ব্যবহৃত priority level।


<br>
<br>

# `#1.2 See the error log within a fix time: `

<br>
<br>


হ্যাঁ, আপনি `journalctl`-এ নির্দিষ্ট তারিখ বা সময় নির্ধারণ করে লগ দেখতে পারেন। উদাহরণস্বরূপ, শেষ ২-৩ দিনের **error logs** দেখতে হলে নিচের মতো কমান্ড ব্যবহার করতে পারেন:

### ১. শেষ দুই দিনের error logs দেখতে:
```bash
journalctl -p err --since "2 days ago"
```

### ২. নির্দিষ্ট তারিখ থেকে error logs দেখতে (উদাহরণ: 2024-12-12):
```bash
journalctl -p err --since "2024-12-12"
```

### ৩. নির্দিষ্ট সময় সীমার মধ্যে (উদাহরণ: 2024-12-12 থেকে 2024-12-14 পর্যন্ত):
```bash
journalctl -p err --since "2024-12-12" --until "2024-12-14"
```

**ব্যাখ্যা:**
- `--since`: কোন সময় থেকে লগ শুরু হবে তা নির্ধারণ করে।
- `--until`: কোন সময় পর্যন্ত লগ দেখাবে তা নির্ধারণ করে।
- `-p err`: শুধুমাত্র error লেভেলের লগ দেখাবে।


<br>
<br>

# `#1.3 একটি প্যাকেজ কীভাবে ইন্সটল হয়েছে (যেমন `pacman` দিয়ে বা ম্যানুয়ালি) এবং সেই প্যাকেজ সম্পর্কিত ত্রুটি (error) লগ:`

<br>
<br>



### **১. প্যাকেজ কীভাবে ইন্সটল হয়েছে তা দেখুন:**
আপনার সিস্টেমে প্যাকেজটি `pacman` দিয়ে ইন্সটল করা হয়েছে কিনা তা চেক করতে:

```bash
pacman -Qi package_name
```

**ব্যাখ্যা:**
- `-Qi`: প্যাকেজ সম্পর্কিত বিস্তারিত তথ্য দেখায় (যদি এটি ইন্সটল করা থাকে)।
- এটি দেখাবে প্যাকেজটি ইন্সটল হয়েছে কিনা, এর ভার্সন, বিল্ড ডেট, এবং আরও অনেক তথ্য।

যদি প্যাকেজটি AUR থেকে ইন্সটল করা হয়, তবে `paru` বা `yay` ব্যবহার করে থাকতে পারেন। এই ক্ষেত্রে AUR প্যাকেজ লিস্ট চেক করতে:

```bash
paru -Qi package_name
# অথবা
yay -Qi package_name
```

---

### **২. প্যাকেজ সম্পর্কিত error logs দেখুন:**
`journalctl` দিয়ে নির্দিষ্ট প্যাকেজ সম্পর্কিত error logs ফিল্টার করতে পারবেন। উদাহরণ:

```bash
journalctl -p err | grep package_name
```

**ব্যাখ্যা:**
- `-p err`: শুধুমাত্র error logs দেখাবে।
- `grep package_name`: নির্দিষ্ট প্যাকেজের সাথে সম্পর্কিত লগগুলো ফিল্টার করবে।

---

### **৩. যদি প্যাকেজ `systemd` সার্ভিসের সাথে যুক্ত হয়:**
যদি প্যাকেজটি কোনো systemd সার্ভিস চালায়, তবে সেই সার্ভিসের লগ দেখতে:

```bash
journalctl -u service_name.service
```

উদাহরণ: যদি প্যাকেজটি `nginx` হয়, তাহলে:

```bash
journalctl -u nginx.service
```

---

### **৪. প্যাকেজ সম্পর্কিত `pacman` লগ চেক করুন:**
`/var/log/pacman.log` ফাইলটি খুলে দেখুন প্যাকেজটি কীভাবে ইন্সটল বা আপডেট হয়েছে:

```bash
cat /var/log/pacman.log | grep package_name
```

---


<br>
<br>

# `#1.4 Service or systemd in arch linux: `

<br>
<br>


Arch Linux-এ **service** বা **servicemd** বলতে মূলত `systemd` সার্ভিসগুলোকে বোঝানো হয়। `journalctl -u service_name.service` কমান্ডটি নির্দিষ্ট একটি সার্ভিসের লগ দেখতে ব্যবহার করা হয়।

### **সার্ভিস কী?**
সার্ভিস হলো এমন একটি ব্যাকগ্রাউন্ড প্রোগ্রাম, যা আপনার সিস্টেমে স্বয়ংক্রিয়ভাবে কোনো কাজ সম্পন্ন করতে পারে। উদাহরণস্বরূপ:
- ওয়েব সার্ভার চালানোর জন্য `nginx.service`।
- ডাটাবেস পরিচালনার জন্য `mariadb.service`।
- ব্লুটুথ পরিচালনার জন্য `bluetooth.service`।

---

### **Arch Linux-এ systemd সার্ভিস সম্পর্কিত কাজ:**

1. **কী কী সার্ভিস চালু আছে বা সক্রিয় (active) আছে তা দেখতে:**
   ```bash
   systemctl list-units --type=service
   ```

2. **নির্দিষ্ট একটি সার্ভিসের স্ট্যাটাস দেখতে:**
   ```bash
   systemctl status service_name.service
   ```
   উদাহরণ: 
   ```bash
   systemctl status bluetooth.service
   ```

3. **সার্ভিস চালু (start) বা বন্ধ (stop) করতে:**
   - চালু করতে:
     ```bash
     sudo systemctl start service_name.service
     ```
   - বন্ধ করতে:
     ```bash
     sudo systemctl stop service_name.service
     ```

4. **সার্ভিস রিস্টার্ট (restart) করতে:**
   ```bash
   sudo systemctl restart service_name.service
   ```

5. **সার্ভিস অটো-স্টার্ট (enable) বা নিষ্ক্রিয় (disable) করতে:**
   - অটো-স্টার্ট চালু করতে:
     ```bash
     sudo systemctl enable service_name.service
     ```
   - অটো-স্টার্ট বন্ধ করতে:
     ```bash
     sudo systemctl disable service_name.service
     ```

<br>
<br>

# `#1.5: Package Related all INFO: `

<br>
<br>


আপনার Arch Linux-এ ইন্সটল করা সমস্ত প্যাকেজের তালিকা দেখতে হলে `pacman` প্যাকেজ ম্যানেজার ব্যবহার করতে পারেন। নিচে বিস্তারিত পদ্ধতি দেওয়া হলো:

---

### **১. ইন্সটল করা সমস্ত প্যাকেজের তালিকা দেখতে:**
```bash
pacman -Q
```

এটি সিস্টেমে ইন্সটল করা সমস্ত প্যাকেজের নাম এবং ভার্সন দেখাবে।

---

### **২. শুধুমাত্র ম্যানুয়ালি ইন্সটল করা প্যাকেজের তালিকা (ডিপেনডেন্সি বাদ দিয়ে):**
```bash
pacman -Qe
```

**ব্যাখ্যা:**
- `-Qe`: শুধুমাত্র "explicitly" ইন্সটল করা প্যাকেজগুলো দেখাবে, যা আপনি সরাসরি ইন্সটল করেছেন।

---

### **৩. শুধুমাত্র ডিপেনডেন্সি হিসেবে ইন্সটল হওয়া প্যাকেজের তালিকা:**
```bash
pacman -Qd
```

**ব্যাখ্যা:**
- `-Qd`: ডিপেনডেন্সি হিসেবে ইন্সটল হওয়া প্যাকেজগুলো দেখাবে।

---

### **৪. নির্দিষ্ট রেপোজিটরি থেকে ইন্সটল হওয়া প্যাকেজ (যেমন, community):**
```bash
pacman -Q | grep community
```

---

### **৫. প্যাকেজ তালিকা একটি ফাইলে সেভ করতে:**
```bash
pacman -Qq > installed_packages.txt
```

এই কমান্ডটি সমস্ত প্যাকেজের নাম একটি `installed_packages.txt` ফাইলে সেভ করবে। পরবর্তীতে এটি অন্য সিস্টেমে প্যাকেজ ইন্সটল করার জন্য কাজে লাগবে।

---

### **৬. শুধু AUR থেকে ইন্সটল করা প্যাকেজ দেখতে (যদি `yay` ব্যবহার করেন):**
```bash
yay -Qm
```

---


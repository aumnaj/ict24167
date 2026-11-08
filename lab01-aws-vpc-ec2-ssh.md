# LAB 01 · Cloud Network & Secure Access

> **ICT24167 Network Management** · สัปดาห์ที่ 1 ของชุด AWS · เวลา 120 นาที · **ทำเป็นกลุ่ม (1 กลุ่ม = 1 AWS Account)** แต่ทุกคนสร้าง EC2 ของตัวเอง
> [← กลับหน้าหลัก](README.md) · [ไป LAB 02 →](lab02-my-first-web-app.md)

## 🎯 จบ Lab นี้แล้วทำอะไรได้

- [ ] สร้างบัญชี AWS แบบ New Experience ที่ **ปลอดภัยตั้งแต่วันแรก** (MFA) และเชิญเพื่อนเข้า Project โดยไม่แชร์รหัสผ่าน
- [ ] ออกแบบและสร้าง **VPC / Subnet / Internet Gateway / Route Table** ด้วยมือ และอธิบายได้ว่าแต่ละชิ้นเทียบกับอุปกรณ์ใดใน Packet Tracer
- [ ] เขียนกฎ **Security Group** ให้เปิดเฉพาะที่จำเป็น และอธิบายว่าทำไมจึงเรียกว่า **Stateful**
- [ ] สร้าง **EC2** และ Remote เข้าไปด้วย **SSH จาก CMD** (และรู้ทางเลือก Instance Connect / PuTTY)
- [ ] ใช้ **Network ACL** พิสูจน์ความต่างของ **Stateless** กับ Stateful ด้วยตาตัวเอง

## ⏱️ แผนเวลา

| Part | หัวข้อ | เวลา |
|---|---|---|
| 0 | สมัคร AWS (New Experience) + MFA + เชิญสมาชิก | 25 นาที |
| 1 | VPC, Subnet, Internet Gateway, Route Table | 25 นาที |
| 2 | Security Group | 10 นาที |
| 3 | สร้าง EC2 Instance | 15 นาที |
| 4 | Remote ด้วย SSH + Break & Fix | 25 นาที |
| 5 | Network ACL: Stateless vs Stateful | 15 นาที |
| 6 | เก็บงาน + Stop Instance | 5 นาที |

## 🧠 กติกา 2 ข้อในห้องนี้

1. **ทำนายก่อนทดสอบ** ทุกครั้งที่เห็นไอคอน 🔮 ให้เขียนคาดการณ์ลงใบบันทึกก่อนกด
2. **เปลี่ยนทีละอย่าง** แล้วทดสอบ (หลักเดียวกับ Ch13 การแก้ปัญหาเครือข่าย)

---

## Part 0 · สมัคร AWS แบบ New Experience และตั้งทีม (25 นาที)

> [!IMPORTANT]
> **1 กลุ่ม = 1 AWS Account (อย่างน้อย)** ให้เลือก **หัวหน้ากลุ่ม 1 คน** เป็นเจ้าของบัญชี แล้วเชิญสมาชิกเข้า Project ด้วยอีเมลของแต่ละคน
> **ห้ามแชร์รหัสผ่านของหัวหน้ากลุ่มให้เพื่อน** ทุกคนต้องเข้าระบบด้วยอีเมลของตัวเอง (หลัก *One person, one identity* เหมือนในบริษัทจริง)

| บทบาท | ใคร | ทำอะไรใน Part 0 |
|---|---|---|
| **Owner** | หัวหน้ากลุ่ม | สมัครบัญชี (0.1) ตั้งค่าความปลอดภัย (0.2) และเชิญสมาชิก (0.3) |
| **Member** | สมาชิกทุกคน | รับคำเชิญทางอีเมลและเข้าระบบด้วยอีเมลของตัวเอง (0.3) ตรวจ Credits และ Region (0.4-0.5) |

### 0.1 สมัครบัญชี (Owner)

1. เปิด <https://aws.amazon.com/> แล้วกด **Create account**
2. เลือกวิธีสมัคร (ใช้ได้ทั้งสองแบบ):

   | วิธี | ใช้กับ |
   |---|---|
   | **Continue with email** | อีเมล `@spumail.com` หรือ `@gmail.com` (ระบบส่งรหัสยืนยันไปทางอีเมล) |
   | **Sign in with Google** | บัญชี `@gmail.com` หรือ `@spumail.com` (ถ้าเป็น Google Workspace) |

3. ทำตามขั้นตอนยืนยันตัวตนบนหน้าจอให้ครบ เมื่อเสร็จ ระบบจะสร้าง **Project แรก** ให้อัตโนมัติภายในไม่กี่วินาที
4. ถ้าหน้าแรกขึ้นกล่อง **Set up Agent Toolkit for AWS** (ตั้งค่า Coding Agent) ให้ **ข้ามไปก่อน** (ใน Lab นี้เราจะสร้างทุกอย่างด้วยมือ เพื่อให้เห็นทุกชิ้นส่วนของเครือข่าย)

📷 ดูภาพตัวอย่างหน้า *Sign up for AWS* และหน้า *Projects* ได้ที่ [AWS News Blog: AWS reimagines the getting started experience](https://aws.amazon.com/blogs/aws/aws-reimagines-the-getting-started-experience/)
<!-- TODO screenshot (ผู้สอน): images/lab01-01-signup.png · images/lab01-02-projects.png -->

> [!NOTE]
> **ไม่ต้องใช้บัตรเครดิต** สำหรับลูกค้าใหม่ส่วนใหญ่ บัญชีจะเริ่มที่ **Free plan** พร้อมเครดิตฟรี ใช้ได้ 6 เดือนหรือจนเครดิตหมด และ **ไม่ถูกเรียกเก็บเงิน** จนกว่าจะอัปเกรดเป็น Paid plan เอง
> ถ้าหน้าจอกลับขอข้อมูลบัตร แปลว่าระบบยังไม่เปิด New Experience ให้บัญชี/อีเมลนั้น หรืออีเมลเคยผูกกับบัญชี AWS มาก่อน **ให้หยุดและแจ้งผู้สอน ห้ามกรอกบัตร**

> [!CAUTION]
> **ห้ามกด Upgrade / Add payment method** และ **ห้ามกด Activate Advanced Features** ถ้าผู้สอนไม่ได้สั่ง ปุ่มหลังนี้เปลี่ยนบัญชีไปเป็นรูปแบบเต็มและ **ย้อนกลับไม่ได้**

### 0.2 ทำให้บัญชีปลอดภัย (Owner)

| ถ้าสมัครด้วย | สิ่งที่ต้องทำ |
|---|---|
| **Google** | เปิด **2-Step Verification** ของบัญชี Google ที่ <https://myaccount.google.com/security> (ใครได้รหัส Google ไป = ได้บัญชี AWS ไปด้วย) |
| **Email** | ไปที่การตั้งค่าบัญชี/ความปลอดภัย (คลิกชื่อบัญชีมุมขวาบน) แล้วเพิ่ม **MFA** ด้วยแอป Authenticator และตั้งรหัสผ่านที่ไม่ซ้ำกับบัญชีอื่น |

> [!IMPORTANT]
> **ทำไมต้องทำ?** บัญชีของ Owner ทำได้ทุกอย่าง รวมถึงสร้างทรัพยากรที่ใช้เครดิตหมดในคืนเดียว ถ้ารหัสผ่านหลุดโดยไม่มี MFA คนอื่นเข้าบัญชีได้ทันที ในบริษัทจริง บัญชีผู้ดูแลที่ไม่มี MFA ถือเป็นข้อบกพร่องร้ายแรงตอน Audit

### 0.3 เชิญสมาชิกเข้า Project

**Owner:**

1. เปิด Project ของกลุ่ม → หน้า **Members** → **Invite** (เชิญสมาชิก)
2. ใส่อีเมล `@spumail.com` หรือ `@gmail.com` ของสมาชิกทีละคน → ส่งคำเชิญ

**Member:**

3. เปิดอีเมลคำเชิญจาก AWS → กดยอมรับ → ตั้งรหัสผ่าน/เปิด MFA ตามขั้นตอนบนหน้าจอ
4. เข้าระบบด้วย **อีเมลของตัวเอง** แล้วตรวจว่าเห็น Project ของกลุ่ม

📷 ภาพตัวอย่างหน้า *Members*: ดูในบทความ [AWS News Blog](https://aws.amazon.com/blogs/aws/aws-reimagines-the-getting-started-experience/) (หัวข้อการเชิญสมาชิก)
<!-- TODO screenshot (ผู้สอน): images/lab01-03-members.png -->

> [!TIP]
> ถ้าคำเชิญไม่มาภายใน 5 นาที ให้ตรวจโฟลเดอร์ **Spam/Junk** ก่อน (อีเมล `@spumail.com` มักถูกกรอง) แล้วให้ Owner ส่งใหม่ ระหว่างรอ ให้นั่งดูหน้าจอ Owner ทำ Part 1 ไปด้วยกันได้

### 0.4 ตรวจเครดิต (ทุกคน)

เปิดหน้า **Billing** ของ Project แล้วจดลงตาราง

| รายการ | ค่า |
|---|---|
| Plan | Free plan |
| Credits คงเหลือ | $ |
| วันคงเหลือของ Free plan | |
| ยอดที่ต้องจ่าย (Balance) | ต้องเป็น **$0.00** |

> [!NOTE]
> เครดิตของบัญชีเดียว **ใช้ร่วมกันทั้งกลุ่ม** ทุกเครื่องที่สมาชิกเปิดทิ้งไว้จะหักจากก้อนเดียวกัน จึงต้อง Stop เครื่องทุกครั้งเมื่อจบคาบ (Part 6)

### 0.5 ตรวจ Region ที่ได้รับ (ทุกคน)

ใน New Experience ระบบ **กำหนด Region ให้ Project อัตโนมัติ** และเปลี่ยนเองจากเมนูปกติไม่ได้ สำหรับห้องเรียนนี้คือ **Asia Pacific (Sydney) `ap-southeast-2`** เมื่อเปิดหน้า VPC หรือ EC2 ให้ดูมุมขวาบนว่าเป็น **Sydney**

| ตรวจ | ค่าที่เห็น |
|---|---|
| Region ของ Project | |
| ตรงกับ `ap-southeast-2` หรือไม่ | |

> [!WARNING]
> ถ้ากลุ่มได้ Region อื่น **ไม่ต้องตกใจ** ทำ Lab ได้เหมือนเดิม แค่เปลี่ยนชื่อ Availability Zone ใน Part 1 (เช่น `ap-southeast-2a` → `<region>a`) และชื่อ Prefix list ใน Part 4.4 ให้ตรงกับ Region ที่ได้ แล้วจดไว้ในใบบันทึก
> ถ้าเปิดหน้า **VPC** หรือ **EC2** ไม่ได้ หรือเมนูหายไป ให้แจ้งผู้สอน **ห้ามกด Activate Advanced Features เอง**

> [!NOTE]
> Sydney อยู่ไกลกว่า Singapore หรือ Region Thailand (`ap-southeast-7`) จึงมี **Latency** สูงกว่า ลองวัดด้วย `Test-NetConnection` หลังสร้าง EC2 ดูว่าตอบช้าแค่ไหน (ใช้เรียนได้สบาย แต่ระบบจริงของลูกค้าไทยมักไม่เลือก Sydney)
> 🔮 **คิดต่อ:** ถ้าเป็นระบบของลูกค้าไทยจริง การเลือก Region มีผลต่ออะไรบ้าง? (Latency, กฎหมาย PDPA / Data Residency, ราคา)

### 0.6 แบ่งงานในกลุ่ม

| งาน | ทำอย่างไร |
|---|---|
| Part 1-2 (VPC, Subnet, Security Group) | **ทำร่วมกัน 1 ชุดต่อกลุ่ม** สลับกันเป็นคนกด (Driver) ทุก Part คนอื่นอ่านขั้นตอนและตรวจ (Navigator) |
| Part 3-4 (EC2, SSH) | **ทุกคนสร้าง EC2 และ Key Pair ของตัวเอง** ตั้งชื่อ `web-<ชื่อเล่น>` และ `key-<ชื่อเล่น>` (ในเอกสารนี้ใช้ `web-01` และ `lab-key` เป็นตัวอย่าง) |
| Part 4.3 และ Part 5 (Break & Fix) | Driver เป็นคนเปลี่ยนกฎ **ทุกคนทดสอบพร้อมกัน** เพราะ Security Group และ NACL ใช้ร่วมกันทั้งกลุ่ม |

> [!TIP]
> **Checkpoint 0** 📸 ภาพหน้า **Members** ที่เห็นสมาชิกครบทุกคน + หน้า **Billing** ที่เห็น Free plan และยอด $0.00 + หลักฐานการเปิด MFA/2-Step Verification ของ Owner โดยปิดบังข้อมูลส่วนตัว (หลักฐาน E1)

---

## Part 1 · VPC และ Subnet: สร้างเครือข่ายของเราเอง (25 นาที)

### 1.0 ภาพรวมสิ่งที่จะสร้าง

```mermaid
flowchart TB
    NET(("🌐 Internet"))
    subgraph VPC["lab-vpc · 10.0.0.0/16"]
        IGW["lab-igw<br/>Internet Gateway"]
        subgraph PUB["public-subnet-1a · 10.0.1.0/24<br/>Route: 0.0.0.0/0 → IGW"]
            EC2["🖥️ EC2 web-01<br/>Ubuntu 24.04<br/>SG: lab-web-sg"]
        end
        subgraph PRI["private-subnet-1a · 10.0.2.0/24<br/>ไม่มี Route ออก Internet"]
            FUT["(ว่างไว้สำหรับ Database ในอนาคต)"]
        end
    end
    YOU["💻 เครื่อง Windows ของเรา"] -- "SSH :22" --> NET
    NET <--> IGW
    IGW <--> EC2
```

| ชิ้นส่วน AWS | ค่าที่ใช้ | เทียบกับใน Packet Tracer |
|---|---|---|
| VPC `lab-vpc` | `10.0.0.0/16` | ทั้งผังเครือข่ายของบริษัท |
| Subnet `public-subnet-1a` | `10.0.1.0/24` | วง DMZ (มีเว็บเซิร์ฟเวอร์) |
| Subnet `private-subnet-1a` | `10.0.2.0/24` | วง LAN ภายใน |
| Internet Gateway `lab-igw` | ผูกกับ `lab-vpc` | ขา WAN ของ Router ที่ต่อออก ISP |
| Route Table `public-rt` | `0.0.0.0/0 → lab-igw` | `ip route 0.0.0.0 0.0.0.0 ...` (Default Route) |

> [!NOTE]
> ทุกบัญชีมี **Default VPC** มาให้แล้ว แต่เราจะ **สร้างเองด้วยมือ** เพื่อให้เห็นทุกชิ้นส่วน (เหมือนต่อสายเองใน PT แทนการเปิดไฟล์สำเร็จรูป)

### 1.1 🔮 ทำนายก่อน: คำนวณ Subnet

| คำถาม | คำตอบของฉัน |
|---|---|
| `10.0.0.0/16` มี IP ทั้งหมดกี่ตัว | |
| `10.0.1.0/24` มี IP ทั้งหมดกี่ตัว | |
| ใน Subnet ปกติ (Ch5) ใช้งานได้จริงกี่ตัว | |
| ใน AWS ใช้ได้จริงกี่ตัว (ดูหลังสร้างเสร็จ ในคอลัมน์ *Available IPv4 addresses*) | |

<details>
<summary>💡 คำใบ้: ทำไม AWS ให้ใช้ได้น้อยกว่า</summary>

AWS จอง IP ไว้ **5 ตัวในทุก Subnet**: ตัวแรก (Network), .1 (VPC Router), .2 (DNS), .3 (สำรองให้ AWS) และตัวสุดท้าย (Broadcast) ลองคำนวณใหม่ดู

</details>

### 1.2 สร้าง VPC

1. ช่องค้นหาพิมพ์ **VPC** → เปิด *VPC Dashboard*
2. เมนูซ้าย **Your VPCs** → **Create VPC**
3. Resources to create: เลือก **VPC only** (ไม่ใช้ *VPC and more*)
4. Name tag: `lab-vpc`
5. IPv4 CIDR: `10.0.0.0/16` · IPv6: **No IPv6 CIDR block** · Tenancy: **Default**
6. **Create VPC**
7. เลือก `lab-vpc` → **Actions › Edit VPC settings** → ติ๊ก ✅ **Enable DNS hostnames** → *Save*

### 1.3 สร้าง Subnet 2 วง

1. เมนูซ้าย **Subnets** → **Create subnet**
2. VPC ID: เลือก `lab-vpc`
3. Subnet 1:
   - Name: `public-subnet-1a`
   - Availability Zone: `ap-southeast-2a`
   - IPv4 subnet CIDR block: `10.0.1.0/24`
4. กด **Add new subnet** สำหรับ Subnet 2:
   - Name: `private-subnet-1a`
   - Availability Zone: `ap-southeast-2a`
   - IPv4 subnet CIDR block: `10.0.2.0/24`
5. **Create subnet**
6. เลือก `public-subnet-1a` → **Actions › Edit subnet settings** → ติ๊ก ✅ **Enable auto-assign public IPv4 address** → *Save*

### 1.4 สร้าง Internet Gateway และผูกกับ VPC

1. เมนูซ้าย **Internet gateways** → **Create internet gateway**
2. Name: `lab-igw` → **Create**
3. **Actions › Attach to VPC** → เลือก `lab-vpc` → **Attach internet gateway**

### 1.5 สร้าง Route Table สำหรับ Public Subnet

1. เมนูซ้าย **Route tables** → **Create route table**
2. Name: `public-rt` · VPC: `lab-vpc` → **Create**
3. แท็บ **Routes** → **Edit routes** → **Add route**
   - Destination: `0.0.0.0/0`
   - Target: **Internet Gateway** → `lab-igw`
   - **Save changes**
4. แท็บ **Subnet associations** → **Edit subnet associations** → ติ๊ก `public-subnet-1a` → **Save**

> [!IMPORTANT]
> **"Public" ไม่ได้มาจากชื่อ** Subnet จะเป็น Public ก็ต่อเมื่อ Route Table ของมันมีเส้นทาง `0.0.0.0/0` ไปที่ Internet Gateway เท่านั้น ส่วน `private-subnet-1a` ยังใช้ *Main route table* ที่มีแค่เส้นทาง `local` จึงออกอินเทอร์เน็ตไม่ได้

<!-- TODO screenshot (ผู้สอน): images/lab01-04-region.png -->

### 1.6 ตรวจทาน

| ตรวจ | ผลที่ต้องเห็น | ✅ |
|---|---|---|
| `public-rt` › Routes | `10.0.0.0/16 → local` และ `0.0.0.0/0 → igw-xxxx` | |
| `public-rt` › Subnet associations | มี `public-subnet-1a` | |
| `lab-igw` › State | **Attached** | |
| Subnets › `public-subnet-1a` | Auto-assign public IPv4 = **Yes** | |

<!-- TODO screenshot (ผู้สอน): images/lab01-05-resource-map.png -->

> [!TIP]
> **Checkpoint 1** 📸 ภาพ *Resource map* ของ `lab-vpc` (เลือก VPC แล้วดูแท็บ **Resource map**) ซึ่งต้องเห็น Subnet 2 วง, Route Table และ IGW เชื่อมกัน (หลักฐาน E2)

---

## Part 2 · Security Group: ไฟร์วอลล์ประจำเครื่อง (10 นาที)

### 2.0 Security Group คืออะไร

| คุณสมบัติ | Security Group | เทียบกับที่เคยเรียน |
|---|---|---|
| ผูกกับอะไร | Network Interface ของ **Instance** | Host Firewall / UFW (Ch11) |
| ประเภทกฎ | **Allow อย่างเดียว** (ไม่มี Deny) | — |
| ค่าเริ่มต้น | Inbound: ปิดหมด · Outbound: เปิดหมด | `ufw default deny incoming` / `allow outgoing` |
| ขากลับ | **อนุญาตอัตโนมัติ (Stateful)** | UFW ที่จำสถานะการเชื่อมต่อ |
| ลำดับกฎ | ไม่มีลำดับ ประเมินทุกกฎรวมกัน | ต่างจาก ACL ที่ใช้กฎแรกที่ตรง (First-match) |

### 2.1 สร้าง Security Group

1. ช่องค้นหาพิมพ์ **EC2** → เมนูซ้าย **Network & Security › Security Groups** → **Create security group**
2. Security group name: `lab-web-sg`
3. Description: `Allow SSH from my IP only`
4. VPC: เลือก **`lab-vpc`** (⚠️ ระวังอย่าเลือก Default VPC)
5. **Inbound rules** → **Add rule**:

   | Type | Protocol | Port | Source | Description |
   |---|---|---|---|---|
   | SSH | TCP | 22 | **My IP** | `ssh from classroom` |

6. **Outbound rules**: คงค่าเดิม (All traffic → `0.0.0.0/0`)
7. **Create security group**

> [!NOTE]
> **My IP** คือ Public IP ที่ AWS มองเห็น ซึ่งเป็น IP ของ **Router ของมหาวิทยาลัย** (ผ่าน NAT ตาม Ch7) ไม่ใช่ IP `192.168.x.x` ของเครื่องเรา ทุกคนในห้องจึงอาจได้ IP เดียวกัน
> ถ้ากลับไปทำที่บ้าน IP จะเปลี่ยน และต้องกลับมาแก้กฎนี้ (เป็นสาเหตุอันดับ 1 ที่ทำให้ SSH จากบ้านไม่ได้)

<!-- TODO screenshot (ผู้สอน): images/lab01-06-sg-inbound.png -->

### 2.2 🔮 ทำนาย (ยังไม่ต้องทดสอบ)

| # | สถานการณ์ (หลังสร้าง EC2 แล้ว) | ทำนาย: ได้ / ไม่ได้ + เหตุผล |
|---|---|---|
| P1 | SSH จากเครื่องในห้อง Lab | |
| P2 | `ping` Public IP ของ EC2 จากเครื่องในห้อง | |
| P3 | เปิด `http://<Public IP>` ในเบราว์เซอร์ | |
| P4 | ใน EC2 สั่ง `sudo apt update` (ออกอินเทอร์เน็ต) ทั้งที่ Outbound ไม่มีกฎสำหรับ "ขากลับ" | |

---

## Part 3 · สร้าง EC2 Instance (15 นาที)

1. EC2 Dashboard → **Instances** → **Launch instances**
2. **Name**: `web-<ชื่อเล่น>` (เช่น `web-oil`) เพื่อให้รู้ว่าเครื่องไหนเป็นของใครในกลุ่ม
3. **Application and OS Images**: **Ubuntu** → **Ubuntu Server 24.04 LTS** (ต้องมีป้าย *Free tier eligible*)
4. **Instance type**: เลือกตัวที่มีป้าย **Free tier eligible** (เช่น `t3.micro`)
5. **Key pair (login)** → **Create new key pair**
   - Key pair name: `key-<ชื่อเล่น>` (ตัวอย่างในเอกสารใช้ `lab-key` ให้แทนด้วยชื่อของตัวเองทุกที่)
   - Key pair type: **RSA**
   - Private key file format: **`.pem`** (สำหรับ CMD/OpenSSH)
   - **Create key pair** → ไฟล์ `lab-key.pem` จะถูกดาวน์โหลด
   - ⚠️ ดาวน์โหลดได้ **ครั้งเดียวเท่านั้น** ถ้าหายต้องสร้าง Key ใหม่ ให้เก็บไว้ใน USB หรือ Cloud Drive ส่วนตัวด้วย (เครื่องในห้อง Lab อาจล้างไฟล์เมื่อ Restart) และ **ห้ามอัปโหลดขึ้น GitHub**

6. **Network settings** → กด **Edit**
   - VPC: **`lab-vpc`**
   - Subnet: **`public-subnet-1a`**
   - Auto-assign public IP: **Enable**
   - Firewall (security groups): **Select existing security group** → `lab-web-sg`
<!-- TODO screenshot (ผู้สอน): images/lab01-07-launch-network.png -->

7. **Configure storage**: ใช้ค่าเริ่มต้น (gp3 8 GiB)
8. **Launch instance** → **View all instances**
9. รอให้ *Instance state* = **Running** และ *Status check* = **3/3 checks passed** (ประมาณ 1-2 นาที)
10. คลิก `web-01` แล้วจดค่าลงตาราง:

| รายการ | ค่า |
|---|---|
| Instance ID | `i-...` |
| Public IPv4 address | |
| Private IPv4 address | `10.0.1.___` |
| Availability Zone | |

> 🔮 **คิดต่อ:** Private IP อยู่ในวงไหน ตรงกับ Subnet ที่เราออกแบบไว้หรือไม่ แล้วเลขตัวสุดท้ายเริ่มที่ .4 ขึ้นไปเพราะอะไร (ย้อนดู 1.1)

> [!TIP]
> **Checkpoint 3** 📸 ภาพหน้า Instance summary ที่เห็น Running, Public IP, Private IP, VPC และ Subnet (หลักฐาน E3)

---

## Part 4 · Remote เข้า EC2 (25 นาที)

### 4.1 วิธีหลัก: SSH ผ่าน CMD / PowerShell (มีใน Windows อยู่แล้ว)

**ขั้นที่ 1 · ตรวจว่ามีคำสั่ง ssh**

```cmd
ssh -V
```

ต้องเห็นเวอร์ชัน เช่น `OpenSSH_for_Windows_9.x`

**ขั้นที่ 2 · ย้ายไปโฟลเดอร์ที่เก็บ Key**

```cmd
cd %USERPROFILE%\Downloads
dir lab-key.pem
```

**ขั้นที่ 3 · ล็อกสิทธิ์ไฟล์ Key ให้เจ้าของอ่านได้คนเดียว**

```cmd
icacls lab-key.pem /inheritance:r
icacls lab-key.pem /grant:r "%USERNAME%:R"
```

> [!NOTE]
> OpenSSH จะ **ปฏิเสธ Key ที่คนอื่นอ่านได้** และขึ้นข้อความ `WARNING: UNPROTECTED PRIVATE KEY FILE!` ขั้นนี้จึงเทียบได้กับ `chmod 400 lab-key.pem` บน Linux/Mac

**ขั้นที่ 4 · เชื่อมต่อ** (เปลี่ยน `13.x.x.x` เป็น Public IP ของตัวเอง)

```cmd
ssh -i lab-key.pem ubuntu@13.x.x.x
```

ครั้งแรกจะถามยืนยัน Fingerprint ให้พิมพ์ `yes`

```text
The authenticity of host '13.x.x.x (13.x.x.x)' can't be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

เมื่อสำเร็จจะเห็น Prompt แบบนี้:

```text
ubuntu@ip-10-0-1-xx:~$
```

> 🔮 สังเกต: ชื่อเครื่อง `ip-10-0-1-xx` คือ IP ใด, Public หรือ Private? เครื่อง EC2 เองรู้จัก Public IP ของตัวเองหรือไม่?

**ขั้นที่ 5 · สำรวจเครื่องจากด้านใน**

```bash
hostname -I            # IP ที่การ์ดเครือข่ายเห็น (มีแต่ Private IP)
ip route               # Default Gateway คือ 10.0.1.1 (VPC Router)
cat /etc/resolv.conf   # DNS ของ VPC
curl -s ifconfig.me    # Public IP ที่โลกภายนอกเห็น
sudo apt update        # ทดสอบออกอินเทอร์เน็ต (ข้อ P4)
```

| คำสั่ง | ผลที่ได้ | ความหมาย |
|---|---|---|
| `hostname -I` | | |
| `ip route` (default via ...) | | |
| `curl -s ifconfig.me` | | |
| `sudo apt update` สำเร็จหรือไม่ | | |

> [!IMPORTANT]
> **Public IP ไม่ได้อยู่บนการ์ดเครือข่ายของ EC2** แต่ Internet Gateway ทำ **1:1 NAT** แปลงให้ (แนวคิดเดียวกับ Static NAT ใน Ch7)

### 4.2 กลับไปเติมผลจริงในตารางทำนาย (Part 2.2)

| # | ทำนาย | ผลจริง | ตรงไหม | อธิบาย |
|---|---|---|---|---|
| P1 SSH | | | | |
| P2 ping | | | | |
| P3 http | | | | |
| P4 apt update | | | | |

<details>
<summary>💡 คำใบ้ P2 และ P4</summary>

- **P2:** `ping` ใช้โปรโตคอล ICMP ซึ่งไม่ใช่ TCP 22 แล้วเรามีกฎ Inbound สำหรับ ICMP หรือยัง?
- **P4:** Security Group "จำ" ได้ว่า EC2 เป็นฝ่ายเริ่มการเชื่อมต่อออกไป แพ็กเก็ตตอบกลับจึงผ่านเข้ามาได้เอง นี่แหละความหมายของ **Stateful**

</details>

### 4.3 💥 Break & Fix: SSH ไม่ได้

**1) Break**: ไปที่ Security Group `lab-web-sg` → **Edit inbound rules** → **ลบ** กฎ SSH → *Save*

**2) Observe**: พิมพ์ `exit` เพื่อออก แล้วสั่ง SSH ใหม่ พร้อมวัดผลด้วย PowerShell

```powershell
ssh -i lab-key.pem ubuntu@13.x.x.x
Test-NetConnection 13.x.x.x -Port 22
```

| สิ่งที่เห็น | บันทึก |
|---|---|
| ข้อความ Error ของ ssh | |
| ใช้เวลาประมาณกี่วินาทีจึงขึ้น Error | |
| `TcpTestSucceeded` | |

**3) Isolate**: ตอบคำถาม

- Error คือ `Connection timed out` หรือ `Connection refused`? แต่ละแบบบอกอะไร (ย้อนดู Ch11 หัวข้อ 11.5 เรื่อง *filtered* กับ *closed*)
- ปัญหาอยู่ที่ **เครื่อง EC2** หรือ **ไฟร์วอลล์**? รู้ได้อย่างไร?

**4) Fix**: เพิ่มกฎ SSH / TCP 22 / **My IP** กลับเข้าไป แล้ว SSH ใหม่ให้สำเร็จ

> [!TIP]
> **Checkpoint 4** 📸 (ก) หน้าจอ SSH ที่เห็น `ubuntu@ip-10-0-1-xx` พร้อมผล `hostname -I` และ `curl ifconfig.me` (ข) ผล `Test-NetConnection` ตอน Break (หลักฐาน E4, E5)

### 4.4 ทางเลือก A: EC2 Instance Connect (ผ่านเบราว์เซอร์)

ใช้เมื่อเครือข่ายบล็อกพอร์ต 22 หรือไม่มีไฟล์ Key อยู่กับตัว

1. Instances → เลือก `web-01` → **Connect** → แท็บ **EC2 Instance Connect** → Username: `ubuntu` → **Connect**
2. 🔮 **คาดว่าจะเชื่อมต่อได้หรือไม่?** ลองแล้วบันทึกผล

<details>
<summary>💡 ทำไมจึงเชื่อมต่อไม่ได้ และแก้อย่างไร</summary>

การเชื่อมต่อนี้ **ไม่ได้มาจาก IP ของเรา** แต่มาจากเครื่องของบริการ EC2 Instance Connect ใน Region นั้น ซึ่งกฎ "My IP" ไม่ครอบคลุม
แก้โดยเพิ่ม Inbound rule: Type **SSH** · Source เลือก **Prefix list** → `com.amazonaws.ap-southeast-2.ec2-instance-connect`
บทเรียน: ต้องรู้เสมอว่า **ใครเป็นต้นทางจริง** ของการเชื่อมต่อ ก่อนเขียนกฎไฟร์วอลล์

</details>

### 4.5 ทางเลือก B: PuTTY (สำหรับทำที่บ้าน)

> [!NOTE]
> เครื่องในห้อง Lab ติดตั้งโปรแกรมไม่ได้ ส่วนนี้จึงให้ไปลองทำเองที่บ้าน

1. ดาวน์โหลด PuTTY จาก <https://www.chiark.greenend.org.uk/~sgtatham/putty/> (เว็บทางการเท่านั้น)
2. เปิด **PuTTYgen** → *Load* `lab-key.pem` → **Save private key** เป็น `lab-key.ppk`
3. เปิด **PuTTY**
   - Session › Host Name: `ubuntu@13.x.x.x` · Port: `22`
   - Connection › SSH › Auth › **Credentials** › Private key file: เลือก `lab-key.ppk`
   - กลับไป Session ตั้งชื่อ `aws-web-01` แล้วกด *Save* → **Open**

### 4.6 เปรียบเทียบ 3 วิธี

| วิธี | ต้องติดตั้ง | ต้องมี Key | ต้องเปิด SG ให้ใคร | เหมาะกับ |
|---|---|---|---|---|
| `ssh` ใน CMD | ไม่ต้อง | ✅ `.pem` | My IP | งานประจำ, Script, ห้อง Lab นี้ |
| EC2 Instance Connect | ไม่ต้อง | ไม่ต้อง | Prefix list ของ AWS | ฉุกเฉิน, เครือข่ายบล็อกพอร์ต 22 |
| PuTTY | ต้อง | ✅ `.ppk` | My IP | ผู้ที่ชินกับ GUI บน Windows |

---

## Part 5 · Network ACL: Stateless vs Stateful (15 นาที)

### 5.0 เทียบให้ชัด

| | Security Group | Network ACL |
|---|---|---|
| ผูกกับ | Instance | **Subnet** |
| กฎ | Allow อย่างเดียว | **Allow และ Deny** |
| ลำดับ | ประเมินทุกกฎรวมกัน | **เรียงตามเลขกฎ น้อยไปมาก หยุดที่กฎแรกที่ตรง** (เหมือน Cisco ACL) |
| ขากลับ | อนุญาตอัตโนมัติ (**Stateful**) | **ต้องเขียนกฎเอง (Stateless)** |

### 5.1 สร้าง NACL ใหม่และผูกกับ Public Subnet

1. VPC Dashboard → **Network ACLs** → **Create network ACL**
2. Name: `lab-public-nacl` · VPC: `lab-vpc` → **Create**
3. เลือก `lab-public-nacl` → แท็บ **Subnet associations** → **Edit** → ติ๊ก `public-subnet-1a` → *Save*

> 🔮 NACL ที่สร้างใหม่ **ไม่มีกฎ Allow เลย** (มีแค่ `*  DENY` ทั้งขาเข้าและขาออก) ตอนนี้ SSH จะเป็นอย่างไร?

### 5.2 เปิดขาเข้าอย่างเดียว

แท็บ **Inbound rules** → **Edit inbound rules** → **Add new rule**

| Rule number | Type | Port | Source | Allow/Deny |
|---|---|---|---|---|
| 100 | SSH (22) | 22 | `0.0.0.0/0` | **Allow** |

**Save** แล้วลอง SSH ใหม่

> 🔮 **ทำนาย:** SSH จะได้หรือไม่ เพราะอะไร? ________________

### 5.3 สืบและแก้

<details>
<summary>💡 คำใบ้ (เปิดหลังลองแล้วเท่านั้น)</summary>

แพ็กเก็ตขาเข้าไปถึง EC2 ได้แล้ว แต่ **คำตอบกลับ** จาก EC2 ออกจาก Subnet ไปที่ **พอร์ตต้นทางของเครื่องเรา** ซึ่งเป็นเลขสูงที่ระบบสุ่ม (Ephemeral port, Ch8) แล้ว Outbound rules ของ NACL ตอนนี้อนุญาตอะไรบ้าง?

</details>

แก้โดยเพิ่ม **Outbound rules**:

| Rule number | Type | Port range | Destination | Allow/Deny |
|---|---|---|---|---|
| 100 | Custom TCP | `1024-65535` | `0.0.0.0/0` | **Allow** |

ทดสอบ SSH ใหม่ ต้องเข้าได้แล้ว

> [!IMPORTANT]
> นี่คือบทเรียนเดียวกับ `established` ใน Cisco ACL (Lab Firewall Part A) **ไฟร์วอลล์แบบ Stateless ไม่รู้ว่าแพ็กเก็ตไหนคือคำตอบ เราจึงต้องเปิดทางกลับเอง** ส่วน Security Group ไม่ต้องทำ

### 5.4 ลำดับกฎสำคัญ

เพิ่ม Inbound rule อีกบรรทัด แล้วใส่ Public IP ของห้อง (ดูจาก <https://checkip.amazonaws.com>) ลงใน Source:

| Rule number | Type | Port | Source | Allow/Deny |
|---|---|---|---|---|
| **90** | SSH (22) | 22 | `<IP ของห้อง>/32` | **Deny** |
| 100 | SSH (22) | 22 | `0.0.0.0/0` | Allow |

> 🔮 SSH ได้หรือไม่? ถ้าเปลี่ยนเลข 90 เป็น 110 ผลจะเปลี่ยนไหม? ลองทั้งสองแบบแล้วบันทึกผล

| Deny ใช้เลขกฎ | ผล SSH | เหตุผล |
|---|---|---|
| 90 | | |
| 110 | | |

### 5.5 คืนค่าเดิม (สำคัญมากสำหรับ LAB 02)

1. **Network ACLs** → เลือก NACL ที่มี *Default = Yes* ของ `lab-vpc`
2. แท็บ **Subnet associations** → **Edit** → ติ๊ก `public-subnet-1a` → *Save* (ย้าย Subnet กลับไปใช้ NACL เริ่มต้นที่อนุญาตทั้งหมด)
3. ลบ `lab-public-nacl` ได้

> [!TIP]
> **Checkpoint 5** 📸 ภาพ Inbound/Outbound rules ของ `lab-public-nacl` ขณะ SSH ได้ และตารางผลข้อ 5.4 (หลักฐาน E6)

---

## Part 6 · เก็บงาน (5 นาที)

1. ใน SSH พิมพ์ `exit`
2. EC2 → Instances → เลือก `web-01` → **Instance state › Stop instance**

> [!WARNING]
> **Stop ไม่ใช่ Terminate** สัปดาห์หน้าเราจะใช้เครื่องนี้ต่อ
> 🔮 หลัง Start ใหม่ **Public IP จะเปลี่ยน** ลองคิดว่าทำไม และถ้าเป็นเว็บจริงของบริษัทจะแก้ปัญหานี้ด้วยอะไร (คำใบ้: Elastic IP + DNS)

> [!NOTE]
> **เครดิตกับค่าใช้จ่าย:** ขณะ Instance ทำงาน AWS คิดค่า EC2 และค่า **Public IPv4** เป็นรายชั่วโมง ซึ่ง Free plan หักจากเครดิต ส่วน Instance ที่ Stop แล้วยังคิดค่า **EBS Storage** อยู่เล็กน้อย จึงต้องตรวจหน้า **Billing › Credits** ทุกสัปดาห์

---

## 🧾 สรุปคำสั่งที่ใช้

| งาน | คำสั่ง (Windows CMD) |
|---|---|
| ตรวจ OpenSSH | `ssh -V` |
| ล็อกสิทธิ์ Key | `icacls lab-key.pem /inheritance:r` และ `icacls lab-key.pem /grant:r "%USERNAME%:R"` |
| SSH | `ssh -i lab-key.pem ubuntu@<Public IP>` |
| ทดสอบพอร์ต (PowerShell) | `Test-NetConnection <Public IP> -Port 22` |
| ดู Public IP ของห้อง | เปิด `https://checkip.amazonaws.com` |

## 🆘 แก้ปัญหาเร็ว

| อาการ | ตรวจอะไรก่อน |
|---|---|
| สมัครแล้วระบบขอบัตรเครดิต | หยุด ห้ามกรอก แจ้งผู้สอน (อีเมลอาจเคยผูกกับบัญชี AWS หรือยังไม่ได้ New Experience) ให้ใช้บัญชีของเพื่อนในกลุ่มแทน |
| สมาชิกไม่ได้รับอีเมลเชิญ | ตรวจ Spam/Junk · Owner พิมพ์อีเมลผิด · ให้ Owner ส่งคำเชิญใหม่ |
| หา VPC / EC2 ที่สร้างไว้ไม่เจอ | ดูมุมขวาบนว่ายังเป็น Region **Sydney** · ตรวจว่าอยู่ใน Project ของกลุ่ม |
| `Connection timed out` | SG ไม่มีกฎ 22 จาก IP ของเรา · My IP เปลี่ยน · NACL ไม่เปิดขาออก 1024-65535 · Subnet ไม่มี Route ไป IGW · Instance ไม่มี Public IP |
| `Permission denied (publickey)` | Username ผิด (Ubuntu ใช้ `ubuntu`) · ใช้ Key ผิดไฟล์ · Instance สร้างด้วย Key อื่น |
| `UNPROTECTED PRIVATE KEY FILE` | ทำขั้น `icacls` ใน 4.1 ขั้นที่ 3 |
| `Identity file not accessible` | ไม่ได้อยู่ในโฟลเดอร์ที่มีไฟล์ Key · พิมพ์ชื่อไฟล์ผิด |
| เลือก `lab-vpc` ใน EC2 ไม่ได้ / SG ไม่ขึ้น | SG ถูกสร้างใน Default VPC ให้สร้างใหม่ใน `lab-vpc` |
| เครือข่ายมหาวิทยาลัยบล็อกพอร์ต 22 | ใช้ EC2 Instance Connect (4.4) |

---

## 📤 การส่งงาน (ส่งเป็นกลุ่ม)

| หัวข้อ | รายละเอียด |
|---|---|
| ผู้ส่ง | **ตัวแทนกลุ่ม 1 คน** ส่งแทนทั้งกลุ่ม |
| รูปแบบ | ไฟล์ **PDF ไฟล์เดียว** |
| ชื่อไฟล์ | `ict24167_awslab1_ชื่อกลุ่ม.pdf` เช่น `ict24167_awslab1_cloudninja.pdf` (แนะนำตั้งชื่อกลุ่มเป็นภาษาอังกฤษตัวเล็ก ไม่มีช่องว่าง) |
| ส่งที่ | อีเมลอาจารย์ **6850018.st@spulive.net** |
| หัวเรื่องอีเมล | `[ICT24167] AWS Lab 1 - ชื่อกลุ่ม` |

**หน้าแรกของ PDF** ต้องมี: ชื่อกลุ่ม · ตารางสมาชิก (รหัสนักศึกษา, ชื่อ-สกุล, หน้าที่ที่ทำ) · ชื่อ Owner ของบัญชี AWS

## 📦 Evidence Pack (100 คะแนน)

หลักฐานที่ **ทำร่วมกัน** (E1, E2, E6) ใส่ชุดเดียวต่อกลุ่ม ส่วนหลักฐานที่ **ทำรายคน** (E3, E4, E5) ต้องมี **ของสมาชิกทุกคน** โดยเขียนชื่อเล่นกำกับใต้ภาพ

| รหัส | หลักฐาน | คะแนน |
|---|---|---|
| E1 | หน้า Members ครบทุกคน + Billing (Free plan, $0.00) + MFA ของ Owner | 10 |
| E2 | Resource map ของ `lab-vpc` + ตารางคำนวณ Subnet 1.1 (ผลจริงจากคอลัมน์ *Available IPv4*) | 15 |
| E3 | Instance summary (Running, Public/Private IP, VPC, Subnet) | 10 |
| E4 | SSH สำเร็จ + ตารางสำรวจ 4.1 ขั้นที่ 5 + ตารางทำนาย P1-P4 พร้อมเหตุผล | 20 |
| E5 | Break & Fix: ข้อความ Error, `Test-NetConnection` และคำตอบ timed out กับ refused | 15 |
| E6 | NACL: กฎขาออก 1024-65535 + ตารางลำดับกฎ 90/110 | 15 |
| E7 | สะท้อนคิด (ด้านล่าง) | 15 |

### ✍️ สะท้อนคิด (E7)

1. อธิบายด้วยคำพูดของตัวเองว่า **Stateful** กับ **Stateless** ต่างกันอย่างไร โดยยกหลักฐานจาก Lab นี้อย่างน้อย 1 อย่าง
2. ถ้าลูกค้าบอกว่า "ขอเปิดพอร์ต 22 ให้ `0.0.0.0/0` ไปเลย จะได้ไม่ต้องแก้กฎบ่อย" จะอธิบายความเสี่ยงและเสนอทางเลือกที่ดีกว่าอย่างไร (3-4 บรรทัด)
3. Public IP ของ EC2 ไม่ได้อยู่บนการ์ดเครือข่ายของเครื่อง แล้วอยู่ที่ไหน? เชื่อมกับเรื่อง NAT ในบทที่ 7

---

[← กลับหน้าหลัก](README.md) · [ไป LAB 02: My First Web App →](lab02-my-first-web-app.md)

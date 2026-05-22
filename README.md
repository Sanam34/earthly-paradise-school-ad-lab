# 🏫 Earthly Paradise School — Active Directory Infrastructure Lab

> A fully functional on-premises IT infrastructure built for **Earthly Paradise Boarding School, Besishahar, Lamjung, Nepal** using Windows Server 2022 and Active Directory Domain Services.

---

## 📌 Overview

This project simulates a **real-world school IT environment** using Active Directory, Group Policy, a Print Server, and an Email Server — all running on a single Windows Server 2022 VM in VMware Workstation.

The domain **EPES.LOCAL** serves 26 staff members across 7 departments with role-based access control, automated wallpapers, shared department folders, a network printer, and individual email accounts.

---

## 🏗️ Architecture

| Component | Details |
|---|---|
| **Hypervisor** | VMware Workstation |
| **OS** | Windows Server 2022 (Desktop Experience) |
| **Domain** | EPES.LOCAL |
| **Domain Controller** | WIN-HIM0ENTMF9C (DC01) |
| **Email Server** | hMailServer |
| **Print Server** | Windows Print and Document Services |
| **Network** | Host-Only VMnet |

---

## 🗂️ Organizational Unit Structure

```
EPES.LOCAL
└── Earthly
    ├── Principal
    ├── Nepali
    │   ├── HOD
    │   └── Teachers
    ├── Science
    │   ├── HOD
    │   └── Teachers
    ├── English
    │   ├── HOD
    │   └── Teachers
    ├── Social
    │   ├── HOD
    │   └── Teachers
    ├── Mathematics
    │   ├── HOD
    │   └── Teachers
    ├── Accountant
    │   ├── HOD
    │   └── Teachers
    └── Library
        ├── HOD
        └── Teachers
```

![OU Structure](screenshots/01-ou-structure.png)

---

## 👥 Users & Roles

### 👑 Principal
| Name | Username | Email |
|---|---|---|
| Uddhav Ghimire | ughimire | ughimire@epes.local |

### 🏫 Heads of Department (HODs)
| Department | Name | Username | Email |
|---|---|---|---|
| Nepali | Sarita Adhikari | sadhikari | sadhikari@epes.local |
| Science | Raj Kumar Shrestha | rshrestha | rshrestha@epes.local |
| English | Ranjita Adhikari | radhikari | radhikari@epes.local |
| Social | Uddhav Ghimire | ughimire | ughimire@epes.local |
| Mathematics | Lal Kumar Shrestha | lshrestha | lshrestha@epes.local |
| Accountant | Surya Gurung | sgurung | sgurung@epes.local |
| Library | Gayatri Sedhai | gsedhai | gsedhai@epes.local |

### 👨‍🏫 Teachers (19 staff)
| Department | Name | Username |
|---|---|---|
| Nepali | Bimala Thapa | bthapa |
| Nepali | Sunita Karki | skarki |
| Nepali | Mina Pandey | mpandey |
| Science | Bikram Rai | brai |
| Science | Pawan Adhikari | padhikari |
| Science | Suman Tamang | stamang |
| English | Priya Sharma | prsharma |
| English | Anita Budhathoki | abudhathoki |
| English | Kopila Gautam | kgautam |
| Social | Ramesh Bhandari | rbhandari |
| Social | Sita Rana | srana |
| Social | Dipak Khanal | dkhanal |
| Mathematics | Nabin Pokharel | npokharel |
| Mathematics | Gita Rijal | grijal |
| Mathematics | Hari Prasad Joshi | hjoshi |
| Accountant | Kabita Ale | kale |
| Accountant | Roshan Basnet | rbasnet |
| Library | Pramila Yonjan | pyonjan |
| Library | Santosh Limbu | slimbu |

![Teachers OU](screenshots/02-teachers-ou.png)
![HOD OU](screenshots/03-hod-ou.png)

---

## 🔐 Group Policy Objects (GPOs)

Three GPOs were created and linked to enforce role-based access control:

### GPO_Principal
- No software or Control Panel restrictions
- Screen lock timeout: 2 hours
- Custom gold & green wallpaper
- Mapped to: `OU=Principal,OU=Earthly,DC=epes,DC=local`

### GPO_HOD
- Control Panel access enabled
- Screen lock timeout: 30 minutes
- Custom green wallpaper
- Mapped to: All 7 HOD OUs

### GPO_Teachers
- Control Panel disabled
- CMD and Registry Editor disabled
- Screen lock timeout: 10 minutes
- Custom light green wallpaper
- Mapped to: All 7 Teachers OUs

![GPO HOD](screenshots/04-gpo-hod.png)
![GPO Teachers](screenshots/05-gpo-teachers.png)
![GPO Principal](screenshots/06-gpo-principal.png)

---

## 🔑 Fine-Grained Password Policies

| Policy | Applied To | Min Length | Max Age | Lockout |
|---|---|---|---|---|
| PSO_Principal | GRP_Principal | 12 chars | 30 days | 3 attempts |
| PSO_HOD | All HOD Groups | 10 chars | 60 days | 3 attempts |
| PSO_Teachers | Domain Users | 8 chars | 90 days | 5 attempts |

---

## 📁 Shared Department Folders

Each department has a dedicated network share with role-based NTFS permissions:

| Share | Path | HOD Access | Teacher Access |
|---|---|---|---|
| Share_Nepali | C:\Shares\Nepali | Modify | Read |
| Share_Science | C:\Shares\Science | Modify | Read |
| Share_English | C:\Shares\English | Modify | Read |
| Share_Social | C:\Shares\Social | Modify | Read |
| Share_Mathematics | C:\Shares\Mathematics | Modify | Read |
| Share_Accountant | C:\Shares\Accountant | Modify | Read |
| Share_Library | C:\Shares\Library | Modify | Read |
| Share_Principal | C:\Shares\Principal | Full Control | No Access |

![Shared Folders](screenshots/07-shared-folders.png)

---

## 🖼️ Role-Based Wallpapers

Custom wallpapers were generated using PowerShell and .NET System.Drawing, automatically applied via GPO:

| Role | Wallpaper | Colors |
|---|---|---|
| Principal | wallpaper_principal.bmp | Deep Green + Gold |
| HOD | wallpaper_hod.bmp | Forest Green + White |
| Teacher | wallpaper_teacher.bmp | Light Green + Grey |

![Wallpapers](screenshots/08-wallpapers.png)

---

## 🖨️ Print Server

A shared network printer was configured and automatically mapped via GPO for all staff:

| Setting | Value |
|---|---|
| Printer Name | SchoolPrinter |
| Share Name | StaffPrinter |
| UNC Path | \\DC01\StaffPrinter |
| Driver | Generic / Text Only |
| Auto-mapped via GPO | Yes (Teachers, HODs, Principal) |

![Print Server](screenshots/09-print-server.png)

---

## 📧 Email Server (hMailServer)

An on-premises email server was configured for all 26 staff members:

| Setting | Value |
|---|---|
| Software | hMailServer (free, open source) |
| Domain | epes.local |
| Total Accounts | 26 |
| Format | firstname_initial + lastname @epes.local |

All accounts follow the format: `username@epes.local`
Example: `ughimire@epes.local`, `sadhikari@epes.local`

![Email Server](screenshots/10-email-server.png)

---

## ⚙️ PowerShell Automation

The entire infrastructure was built using PowerShell scripts:

| Script | Purpose |
|---|---|
| `EPES_AD_Setup.ps1` | Creates OU structure, users, security groups |
| `EPES_GPO_Setup.ps1` | Creates GPOs, shared folders, wallpapers, password policies |

Key modules used:
- `ActiveDirectory`
- `GroupPolicy`
- `System.Drawing` (wallpaper generation)
- `SmbShare` (network shares)

---

## 🧪 Skills Demonstrated

- ✅ Active Directory Domain Services (AD DS)
- ✅ Organizational Unit (OU) Design
- ✅ Role-Based Access Control (RBAC)
- ✅ Group Policy Objects (GPO)
- ✅ Fine-Grained Password Policies (PSO)
- ✅ NTFS & SMB Share Permissions
- ✅ Print Server Configuration
- ✅ Email Server Setup (hMailServer)
- ✅ PowerShell Automation & Scripting
- ✅ Windows Server 2022 Administration
- ✅ VMware Workstation Virtualization

---

## 🏫 About the School

This lab is based on **Earthly Paradise Boarding School**, located in Besishahar, Lamjung, Nepal. The infrastructure was designed to reflect the school's real departmental structure including academic departments (Nepali, Science, English, Social, Mathematics) and support departments (Accountant, Library).

---

## 📬 Connect

Built by a passionate IT learner as a homelab project to demonstrate enterprise Active Directory skills.

> *"Built with real intent — simulating real infrastructure for a real school."*

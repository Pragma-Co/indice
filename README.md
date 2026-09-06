<div align="center">

  <h2>Índice</h2>

  [Challenge Description](#challenge-description) |
  [Product Backlog](#product-backlog) |
  [Project Evolution Timeline & Sprints](#project-evolution-timeline--sprints) |
  [Technologies Used](#technologies-used) |
  [Project Structure](#project-structure) |
  [How to Execute the Project](#how-to-execute-use-and-test-the-project) |
  [DoR and DoD](/documentation/DoR%20&%20DoD/README.md) |
  [User Guide](#user-guide) |
  [Installation Guide](#installation-guide) |
  [Team](#team)

</div>

> **Project Status:** Sprint 1 in Progress
> 
> **Sprints Schedule:** 3 Sprints Total
> 
> **Documentation Directory:** [Link](./documentation/)
> 
> **Project Video:** [YouTube](https://link-do-seu-video.com)


## Challenge Description <a id="challenge-description"></a>

Technical document management is a time-consuming activity due to the volume and complexity of the concepts involved. Currently, professionals must read documents and manually associate them with technical standards and similar files within company databases, as well as assign labels to facilitate search and retrieval. Being entirely manual, this process requires extensive work hours and is prone to errors.

### Akaer: Industrial Partner

**Akaer** is an engineering and technology company operating in the aerospace, defense, and industrial sectors.

The core challenge consists of optimizing the reading, cross-referencing, and interpretation of complex documentation across various domains (legal, technical, normative, and qualitative), reducing analysis time and accelerating decision-making securely.

<br>

> Ingestion and processing of multiple formats (PDF, Word, Excel, etc.);  
> Automated data cross-referencing via Artificial Intelligence;  
> Strict access control and security (Intranet, download/copy restrictions);  
> Management of technical standards, international acronyms (such as FAA), and LGPD compliance.

<br>

## Functional Requirements

| **ID** | **Feature** | **Description** |
|:------:|:------------------------------------|:-------------------------------------------------------------------------------------------------------|
|  RF1   | **Multidimensional Search & Filtering** | Perform text-based and structured searches across relational and NoSQL databases using sequential filters (sub-groups, categories, tags) and advanced criteria. |
|  RF2   | **Document Ingestion & Processing** | Enable uploading, extraction, and pre-processing of multiple formats (PDF, Excel, Word) for technical, legal, normative, and qualitative documentation to be analyzed by AI. |
|  RF3   | **AI Analysis & Summarization** | Employ AI models to categorize documents, cross-reference data across different areas, generate automated summaries, and suggest optimal documents for the user's context. |
|  RF4   | **Access Management & Permissions** | Control corporate access strictly via Intranet, enforcing security levels by department and managing smart notifications for restricted documents with approval routing. |
|  RF5   | **Audit Trail & Compliance** | Ensure information security through audit trails, encryption, access logs, data anonymization, and adherence to data protection regulations (LGPD). |
|  RF6   | **Smart Navigation & Notifications** | Provide usability features such as movement history, document saving to user profiles (without download/export permissions), search history, and alerts for new standard versions. |

---


<br>


## Product Backlog <a id="product-backlog"></a>

| Rank | Priority | User Story / Requirement | Story Points | Sprint | Client Requirement | Status |
| :---: | :---: | :--- | :---: | :---: | :---: | :---: |
| -- | -- | *(Base table placeholder for future entries)* | -- | -- | -- | -- |

---

<br>

## Project Evolution Timeline & Sprints <a id="project-evolution-timeline--sprints"></a>

| Sprint | Period | Sprint Documentation | Delivered Increment YouTube Video |
| :---: | :---: | :--- | :--- |
| **Sprint 1** | 07/09 - 27/09 | [Sprint 1 Documentation](./documentation/process/sprints/sprint-1/README.md) | [YouTube](link_video_sprint_1) |
| **Sprint 2** | 05/10 - 25/10 | [Sprint 2 Documentation](./documentation/process/sprints/sprint-2/README.md) | [YouTube](link_video_sprint_2) |
| **Sprint 3** | 02/11 - 22/11 | [Sprint 3 Documentation](./documentation/process/sprints/sprint-3/README.md) | [YouTube](link_video_sprint_3) |

---

<br>

## Technologies Used <a id="technologies-used"></a>

<p align="center">
  <a href="https://skillicons.dev">
    <img src="https://skillicons.dev/icons?i=python,django,postgres,mongodb,vue,typescript,git,github,githubactions,figma,vscode,docker&theme=light" />
  </a>
</p>

<br>

## How to Execute the Project <a id="how-to-execute-the-project"></a>


###  Prerequisites

- Git ([Download](https://git-scm.com/downloads))
- Docker Desktop 4.30+ ([Download](https://www.docker.com/products/docker-desktop/))
- Node.js 20.19+ ([Download](https://nodejs.org/en/download))

> **Windows:** We recommend using the WSL2 backend with Docker Desktop.

---

### 1. Clone the Repositories

Clone both repositories:

**Backend:**

```bash
git clone https://github.com/Pragma-Co/backend-api-6.git
```

**Frontend:**

```bash
git clone https://github.com/Pragma-Co/frontend-api-6.git
```

---

### 2. Backend Setup

Navigate to the backend directory:

```bash
cd backend-api-6
```

**1. Generate the `.env` file:**

**Linux/macOS:**

```bash
docker run --rm --user "$(id -u):$(id -g)" -v "$(pwd):/app" -w /app python:3.12-slim python scripts/setup_env.py
```

**Windows (PowerShell):**

```powershell
docker run --rm -v "${PWD}:/app" -w /app python:3.12-slim python scripts/setup_env.py
```

**2. Start the backend:**

```bash
docker compose up --build
```

**Expected Output:**

<br>

Backend running at `http://localhost:8000`.

To verify that the backend and databases are running correctly, access:

`http://localhost:8000/health/`

---

### 3. Frontend Setup

Open another terminal and navigate to the frontend directory:

```bash
cd frontend-api-6
```

**1. Create the `.env` file:**

**Linux/macOS:**

```bash
cp .env.example .env
```

**Windows (PowerShell):**

```powershell
Copy-Item .env.example .env
```

> Make sure `VITE_API_PORT=8000` is configured in the `.env` file.

**2. Install the dependencies:**

```bash
npm install
```

**3. Start the application:**

```bash
npm run dev
```

**Expected Output:**

<br>

Frontend running at `http://localhost:5173`.

---

### 4. Accessing Índice

With both the backend and frontend running, access:

`http://localhost:5173/`

The **Índice** application is now ready to use.


---

<br>

## Team <a id="team"></a>

<div align="center">
  <table>
    <tr>
      <th>Member</th>
      <th>Role</th>
      <th>GitHub</th>
    </tr>
    <tr>
      <td><b>Alexander Lima</b></td>
      <td>Dev Team</td>
      <td><a href="https://github.com/netizenhub"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>Augusto Piatto</b></td>
      <td>Dev Team</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/augustopiatto"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>Davi Soares</b></td>
      <td>Dev Team</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/DaviSFS21"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>Isabelly Rodrigues</b></td>
      <td>Product Owner</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/61isabelly"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>João Bispo</b></td>
      <td>Dev Team</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/BispoJPM"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>Luiz Ferreira</b></td>
      <td>Dev Team</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/LuizHRFerreira"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
    <tr>
      <td><b>Tiago Reis</b></td>
      <td>Scrum Master</td>
      <td><a href="https://github.com/orgs/Pragma-Co/people/TiagoTReis"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"></a></td>
    </tr>
  </table>
</div>

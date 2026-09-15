<div align="center">

  <h2>Índice</h2>

  <p align="center">
    <img src="./assets/header.png" alt="Header do Projeto" width="100%">
  </p>


  [Challenge Description](#challenge-description) |
  [Functional Requirements](#functional-requirements) |
  [Product Backlog](#product-backlog) |
  [Project Evolution Timeline & Sprints](#project-evolution-timeline--sprints) |
  [Technologies Used](#technologies-used) |
  [How to Execute the Project](#how-to-execute-the-project) |
  [DoR and DoD](./documentation/DoR%20%26%20DoD/README.md) |
  [Team](#team)

</div>

> **Project Status:** Sprint 1 in Progress
>
> **Sprints Schedule:** 3 Sprints Total
>
> **Documentation Directory:** [Link](./documentation/)
>
> **Project Video:** [YouTube](https://link-do-seu-video.com) <!-- TODO: substituir pela URL real -->

---

<br>

## Challenge Description

Technical document management is a time-consuming activity due to the volume and complexity of the concepts involved. Currently, professionals must read documents and manually associate them with technical standards and similar files within company databases, as well as assign labels to facilitate search and retrieval. Being entirely manual, this process requires extensive work hours and is prone to errors.

## Akaer: Industrial Partner

**Akaer** is an engineering and technology company operating in the aerospace, defense, and industrial sectors.

The core challenge consists of optimizing the reading, cross-referencing, and interpretation of complex documentation across various domains (legal, technical, normative, and qualitative), reducing analysis time and accelerating decision-making securely.

## Proposed Solution

To eliminate manual bottlenecks and mitigate compliance risks, our team is developing **Índice**, an intelligent document orchestration and analysis platform. The solution addresses the challenge through three foundational pillars:

* **Centralized Ingestion & Pipeline Processing:** Standardizes file intake, parsing unstructured data and structuring core technical metadata automatically into hybrid database storage (PostgreSQL and MongoDB).
* **AI-Assisted Knowledge Discovery:** Uses contextual AI models to automatically classify incoming files, cross-reference technical standards across domains, generate multi-document executive summaries, and surface relevant excerpts via semantic and multi-parameter search.
* **Enterprise Governance & Security:** Restricts access through role-based permissions (RBAC), provides in-app permission workflows for sensitive files, blocks unauthorized external data exfiltration, and maintains immutable audit logs in compliance with LGPD standards.

---

<br>

## Functional Requirements

| **ID** | **Feature** | **Description** |
|:------:|:------------------------------------|:-------------------------------------------------------------------------------------------------------|
| RF1 | **Multidimensional Search & Filtering** | Deliver basic attribute filtering in Sprint 1 to isolate relevant files rapidly, expanding in Sprint 2 to deep content querying and semantic search across technical documentation (US2, US3, US8). |
| RF2 | **Document Ingestion & Processing** | Enable uploading, standardized metadata extraction, and in-browser overview visualization in Sprint 1, ensuring all incoming files are validated and stored in a unified repository (US1, US2, US4, US6). |
| RF3 | **AI Analysis & Summarization** | Automate document classification starting in Sprint 1, delivering cross-referencing, automated executive summaries, and intelligent thematic clustering by Sprint 2 (US5, US6, US8, US9, US10). |
| RF4 | **Access Management & Permissions** | Restrict document visualization and implement approval routing by Sprint 3, allowing managers to define departmental access groups and users to request clearance directly in-app (US12, US13). |
| RF5 | **Audit Trail & Governance** | Provide real-time operational monitoring of file ingestion in Sprint 1, establishing comprehensive access tracking and audit logs to ensure regulatory integrity (US7). |
| RF6 | **Smart Navigation & Personal Workspace** | Allow users to organize personal libraries and inspect thematic clusters during Sprint 2, streamlining recurring access without modifying base repository structures (US10, US11). |

---

<br>

## Product Backlog

| Rank | Priority | User Story / Requirement | Story Points | Sprint | Client Requirement | Status |
| :---: | :---: | :--- | :---: | :---: | :---: | :---: |
| 1 | High | As an employee, I want to upload documents so that I can find all information in a single place. | 5 | 1 | RF2 | Doing |
| 2 | High | As an employee, I want documents to follow a standard so that I can easily track and identify each one. | 8 | 1 | RF2, RF1 | Doing |
| 3 | High | As an employee, I want to filter documents by simple criteria so that I can quickly find everything relevant to me. | 5 | 1 | RF1 | Doing |
| 4 | High | As an employee, I want to view the document in an overview format to better understand and read it. | 2 | 1 | RF2 | Doing |
| 5 | High | As an employee, I want uploaded documents to be automatically classified so that I do not need to adjust them manually, allowing me to compare and reuse information reliably. | 8 | 1 | RF3 | Doing |
| 6 | Medium | As a technical manager, I want to access document uploads and access requests to ensure integrity, security, and standardization of the repository. | - | 1 | RF2, RF3 | Doing |
| 7 | Medium | As a technical manager, I want to view how documents are being uploaded and updated to know that everything is working properly. | - | 1 | RF5 | Doing |
| 8 | Medium | As an employee, I want to search terms and questions within document contents and receive suggestions of excerpts and related documents to quickly reach what is most relevant. | - | 2 | RF1, RF3 | To Do |
| 9 | Medium | As an employee, I want to receive a simplified summary of the topic I am searching, using retrieved documents, to have an initial guidance before reading in detail. | - | 2 | RF3 | To Do |
| 10 | Low | As an employee, I want to see document clusters by topic or subject to explore sets of documents without having to organize everything manually. | - | 2 | RF3, RF6 | To Do |
| 11 | Low | As an employee, I want to save documents to my personal library to easily access them later without repeating the search. | - | 2 | RF6 | To Do |
| 12 | Low | As a technical manager, I want to create access groups and assign users to these groups (by department and security level) to control who can view or edit each set of documents. | - | 3 | RF4 | To Do |
| 13 | Low | As an employee, I want to request access to a restricted document directly on the viewing screen so that I do not need to look for another way to request authorization. | - | 3 | RF4 | To Do |

---

<br>

## Project Evolution Timeline & Sprints

| Sprint | Period | Sprint Documentation | Delivered Increment YouTube Video |
| :---: | :---: | :--- | :--- |
| **Sprint 1** | 07/09 - 27/09 | [Sprint 1 Documentation](./documentation/process/sprints/sprint-1/README.md) | _Soon_ |
| **Sprint 2** | 05/10 - 25/10 | [Sprint 2 Documentation](./documentation/process/sprints/sprint-2/README.md) | _Soon_ |
| **Sprint 3** | 02/11 - 22/11 | [Sprint 3 Documentation](./documentation/process/sprints/sprint-3/README.md) | _Soon_ |

<!-- TODO: trocar "Em breve" por [YouTube](URL) conforme os vídeos forem publicados -->

---

<br>

## Technologies Used

<p align="center">
  <a href="https://skillicons.dev">
    <img src="https://skillicons.dev/icons?i=python,django,postgres,mongodb,vue,typescript,git,github,githubactions,figma,vscode,docker&theme=light" alt="Tecnologias utilizadas" />
  </a>
</p>

---

<br>

## How to Execute the Project

### Prerequisites

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

<!-- TODO: inserir print ou bloco de saída do terminal, ex.: ![Backend output](./assets/backend-output.png) -->

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

<!-- TODO: inserir print ou bloco de saída do terminal, ex.: ![Frontend output](./assets/frontend-output.png) -->

Frontend running at `http://localhost:5173`.

---

### 4. Accessing Índice

With both the backend and frontend running, access:

`http://localhost:5173/`

The **Índice** application is now ready to use.

---

<br>

## Team

<div align="center">
  <table>
    <tr>
      <th>Identification</th>
      <th>Name</th>
      <th>Role</th>
      <th>Networking</th>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Alexander.png" alt="Alexander Lima" width="120" height="120"></td>
      <td><b>Alexander Lima</b></td>
      <td>Dev Team</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/alexander-silva-lima-96a0432a6/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/netizenhub"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Augusto.jpeg" alt="Augusto Piatto" width="120" height="120"></td>
      <td><b>Augusto Piatto</b></td>
      <td>Dev Team</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/augusto-piatto/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/augustopiatto"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Davi.jpeg" alt="Davi Soares" width="120" height="120"></td>
      <td><b>Davi Soares</b></td>
      <td>Dev Team</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/dsf21/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/DaviSFS21"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Isabelly.png" alt="Isabelly Rodrigues" width="120" height="120"></td>
      <td><b>Isabelly Rodrigues</b></td>
      <td>Product Owner</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/isabelly-rdgs/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/61isabelly"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Joao.jpeg" alt="João Bispo" width="120" height="120"></td>
      <td><b>João Bispo</b></td>
      <td>Dev Team</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/jo%C3%A3o-pedro-563369181/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/BispoJPM"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Luiz.png" alt="Luiz Ferreira" width="120" height="120"></td>
      <td><b>Luiz Ferreira</b></td>
      <td>Dev Team</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/luiz-henrique-rabello-ferreira-3600752ba/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/LuizHRFerreira"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
    <tr>
      <td align="center"><img src="./assets/Foto%20-%20Tiago.png" alt="Tiago Reis" width="120" height="120"></td>
      <td><b>Tiago Reis</b></td>
      <td>Scrum Master</td>
      <td align="center">
        <a href="https://www.linkedin.com/in/tiago-torres-dos-reis/"><img src="https://img.shields.io/badge/Linkedin-blue?logo=Linkedin&logoColor=white" alt="LinkedIn" height="28"></a><br>
        <a href="https://github.com/TiagoTReis"><img src="https://img.shields.io/badge/GitHub-111217?logo=github&logoColor=white" alt="GitHub" height="28"></a>
      </td>
    </tr>
  </table>
</div>

<p align="center">
  <img src="./assets/footer.png" alt="Footer do Projeto" width="100%">
</p>
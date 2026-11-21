# IIS HTTP Redirection Lab (`lab.com` → `tech.com`, 307 Temporary) — Windows Server 2025 Simulator

[![Launch Interactive Lab](https://img.shields.io/badge/Click_to_Launch-Interactive_Lab_(lab20.html)-0078D7?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab20.html)

### 🔀 Windows Server IIS HTTP Redirection (307 Temporary) — Interactive Lab (`lab20.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab20.html>

An interactive, single-file web-based **Windows Server 2025 Simulator** designed for hands-on **Internet Information Services (IIS)** administration training. With both the old site (`lab.com`) and new site (`tech.com`) pre-hosted on `SMEC-DC1` (`smec.com`), this lab guides learners step-by-step through verifying the old site in Microsoft Edge, opening **IIS Manager (`inetmgr`)**, configuring **HTTP Redirect** on `lab.com` to forward traffic to `http://tech.com` using status code **Temporary (307)**, and observing the live `HTTP/1.1 307 Temporary Redirect` browser trace.

## Table of Contents

1. [What is HTTP Redirection in IIS?](#what-is-http-redirection-in-iis)
2. [Understanding IIS Redirect Status Codes (301 vs. 302 vs. 307)](#understanding-iis-redirect-status-codes-301-vs-302-vs-307)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 6-Step Lab Walkthrough](#complete-6-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

## What is HTTP Redirection in IIS?

**HTTP Redirection** is an IIS Role Service (under *Web Server → Common HTTP Features*) that automatically sends visitors requesting one URL (such as an old domain `http://lab.com`) to a different destination URL (such as `http://tech.com`).

When a browser requests `http://lab.com`, IIS responds with a `3xx` redirect status code and a `Location: http://tech.com` header. The browser automatically follows that `Location` header and loads the new website seamlessly.

## Understanding IIS Redirect Status Codes (301 vs. 302 vs. 307)

IIS Manager provides three built-in status codes inside the **HTTP Redirect** feature:

| Status Code | IIS Label | Behavior & HTTP Method Handling | Best Use Case |
| ----- | ----- | ----- | ----- |
| **307** *(Used in this Lab)* | **Temporary (307)** | Temporarily redirects clients to the new URL while **strictly preserving the original HTTP method** (`GET` stays `GET`, `POST` stays `POST`). Browsers and search engines do **not** permanently replace the old URL. | Planned maintenance, temporary site migrations, or testing a new site (`tech.com`) before permanently retiring the old site (`lab.com`). |
| **302** | **Found (302)** | Temporary redirect, but older clients may change a `POST` request into a `GET` request when following the redirect. | Legacy temporary redirects where strict HTTP method preservation is not required. |
| **301** | **Permanent (301)** | Tells browsers and search engines that the old URL has **permanently moved** to the new URL and caches the redirect. | Permanent domain rebranding or retiring an old domain forever. |

## Lab Environment Specifications

| Component | Details |
| ----- | ----- |
| **Server Hostname** | `SMEC-DC1` (`192.168.1.1`) — Windows Server 2025 |
| **Active Directory Domain** | `smec.com` |
| **Pre-installed Roles** | AD DS, DNS Server, Web Server (IIS) + **HTTP Redirection** |
| **Old Website (Source)** | `http://lab.com` (`C:\inetpub\lab`, Port `80`) |
| **New Website (Destination)** | `http://tech.com` (`C:\inetpub\tech`, Port `80`) |
| **Redirect Behavior** | Redirect all requests to exact destination (`http://tech.com`) |
| **HTTP Status Code** | **Temporary (307)** |

## Simulator Interface & Features

The simulator (`lab20.html`) provides a focused Windows Server 2025 IIS desktop environment:

1. **Left Sidebar (Server Card, Progress Checklist & Concept Box):**
   * Tracks all 5 lab milestones (`Test Old Site`, `Open IIS Manager`, `Select lab.com → Redirect`, `Set 307 & Apply`, and `Verify Live 307 Redirect`) in real time.
   * Displays a **"What is HTTP 307 Temporary Redirect?"** quick-reference card, lab parameters, and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **IIS Manager (`inetmgr`):** Accessible via Desktop icon, **Server Manager → Tools → IIS Manager**, or **Start → Run → `inetmgr`**. Features the authentic three-pane IIS layout (*Connections*, *Feature Tiles*, and *Actions*), pre-configured `lab.com (Old)` and `tech.com (New)` sites, and the complete **HTTP Redirect** configuration panel.
   * **Microsoft Edge Browser with Live HTTP Trace:** Displays the exact request/response sequence (`GET http://lab.com/` → `HTTP/1.1 307 Temporary Redirect` → `Location: http://tech.com` → `200 OK`) and automatically updates the browser address bar to `http://tech.com`.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, IIS settings, or browser controls.
   * Tracks progress from **Step 1 / 6** to **100% Complete**.

## Complete 6-Step Lab Walkthrough

### Phase 1: Verify Pre-Redirect Behavior (`Microsoft Edge`)

1. **Test Old Site (`http://lab.com`):** Open **Microsoft Edge** on the Desktop and navigate to `http://lab.com`. Confirm it returns `200 OK` and loads the old `lab.com` website directly.

### Phase 2: Configure HTTP 307 Redirection in IIS (`inetmgr`)

2. **Open IIS Manager:** Click **IIS Manager** on the Desktop (or **Server Manager** → **Tools** → **IIS Manager**, or `Start` → `Run` → `inetmgr`).
3. **Select the Old Site:** In the left **Connections** tree under **Sites**, click **`lab.com (Old)`**.
4. **Open HTTP Redirect:** In the center pane of **lab.com Home**, double-click the **🔀 HTTP Redirect** icon.
5. **Configure 307 Temporary Redirect & Apply:**
   * Check **Redirect requests to this destination** and enter `http://tech.com` *(or `http://www.tech.com`)*.
   * Check **Redirect all requests to exact destination (instead of relative to destination)**.
   * Change **Status code** from `Found (302)` to **`Temporary (307)`**.
   * Click **✓ Apply** in the right **Actions** pane.

### Phase 3: Verify Live 307 Redirection (`Microsoft Edge`)

6. **Verify Redirect in Browser:** Open **Microsoft Edge** and navigate to `http://lab.com`.
   * Observe the live HTTP trace showing **`HTTP/1.1 307 Temporary Redirect`** with `Location: http://tech.com`.
   * Confirm the address bar automatically changes to `http://tech.com` and loads the new **tech.com** website, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

### Exact Destination vs. Relative Destination

* **Exact Destination Checked:** Every request to `lab.com`—including subpages like `http://lab.com/about.html`—is sent strictly to `http://tech.com`. Useful when the new site has a completely different folder structure.
* **Exact Destination Unchecked (Relative):** Requests to `http://lab.com/about.html` are forwarded to `http://tech.com/about.html`. Useful when both sites share the same page paths.

### Prerequisite Role Service

The **🔀 HTTP Redirect** icon only appears in IIS Manager if the **HTTP Redirection** role service (under *Web Server (IIS) → Web Server → Common HTTP Features*) has been installed via Server Manager.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab20.html**

### Run Locally (Offline)

1. Download `lab20.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, SVG Microsoft Edge icon, live progress badges)
* **Vanilla JavaScript** (6-step validation engine, interactive IIS Manager & HTTP Redirect form, and simulated Microsoft Edge browser with live HTTP 307 header tracing)
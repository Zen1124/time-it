# 1. Overview

This system is designed to streamline employee time tracking, approval, and payroll integration for Isaac Group LLC where employees log hours and managers (external to the company) must approve them. It solves inefficiencies with paper/email submissions and ensures compliance with approval processes.

# 2. Goals & Requirements
### Timesheet Functional Requirements

1. Employee Timesheet Submission
    - Employees log in hours
    - Pay rate can be adjusted (OT or Supervisor)
    - Entry through simple UI (mobile friendly)
2. Approval Workflow
    - After entry into system, send email to supervisor for approval
    - Email should have quick approve/deny buttons
    - Option to email for concerns/inconsistencies
3. Data Processing
    - Approved hours export to CSV/Excel format
    - Prepare invoice with exported data
4. Login and Reporting
    - Employees and admin should be able to login using email and provided passwords
    - Admin should be able to view all records of timesheets and filter by employee and date
5. Notifications
    - Email notification sent to employee email after submission and approval
    - Nudging notification sent to external manager if no response in 3 days

### Timesheet Non Functional Requirements

1. Ease of Use
    - Designed for non-technical employees (nurses, staff with limited tech skills).

2. Scalability
    - Handle multiple departments and clients.

3. Security
    - External managers should only see the timesheets assigned to them.
    - Employees should only see the timesheets assigned to them

4. Cost Efficiency
    - Low-cost cloud hosting
    - Integration with existing systems (e.g., n8n + Google Workspace).

# 3. Systems Architecture

### Frontend
- Web app (React/Vite) for employees and managers.
- Mobile-friendly design.
- Authentication (OAuth for employees, secure token links for external managers).
### Backend
- API server (Fastify/Express).
- Business logic for timesheet validation and approval flow.
### Database
- PostgresSQl via Prisma
- employees (profile, department, rates).
- Key Tables:
    - `employees` (profile, location, rates)
    - `managers` (email, contact info).
    - `timesheets` (entries, status).
    - `approvals` (manager actions, timestamp).
### Integration Layer
- n8n workflows for:
    - Auto-export to Google Sheets.
    - Notification triggers (email/SMS).

# 4. Data Flow

### Logging in Hours
1. Employee submits hours (via web form).
2. System creates a `timesheet` record with status = `"Pending"`.

3. Approval workflow triggers:
    - External managers receive secure email link → `approve/reject`.
4. Manager interaction:
    - Once approved:
        - Timesheet status = `"Approved"`.
        - Create entry in `approvals` table
        - Data auto-exported to payroll sheet/system.
    - If rejected:
        - Employee notified, timesheet returned for correction.

# 5. Example JSON Format
```
{
  "employee_id": "11034",
  "name": "Dagatan, Boots",
  "period": "2025-07-13 to 2025-07-19",
  "entries": [
    {
      "date": "2025-07-13",
      "in": "22:18",
      "out": "06:51",
      "worked_total": 8.5,
      "codes": [
        { "type": "Reg", "hours": 8.0 },
        { "type": "RN-LPN-NIG", "hours": 7.5 }
      ]
    }
  ],
  "manager_id": "ext-502",
  "status": "Pending"
}
```

# 6. Risks & Mitigations

- External Managers ignoring approvals   
    - Mitigation: Reminder notifications & escalations.

- Employee error in entry 
    - Mitigation: Validations (max hours per shift, role-based rates).

# 7. Roadmap
- Sprint 1 (2 weeks): Employee submission UI + DB schema.

- Sprint 2 (2 weeks): Manager approval workflow + email notifications.

- Sprint 3 (2 weeks): n8n & Google Sheets integration.

- Sprint 4 (2 weeks): Payroll export, reporting & audit logs.

- Sprint 5 (optional): Mobile app polish.
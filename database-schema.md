# Database Schema

## Tables
### `employees`
<b>Keys:</b>

```
employee_id, first_name, last_name, facility_id, position, manager_id
```
<b> Primary Key: </b> `employee_id`

<b> Relations: </b> 
- Has many `timesheets`
- Belongs to one `facilities`
- Belongs to one `users`
- Has many `payrates`
- Has one `manager`
    
### `managers`
<b>Keys:</b>

    manager_id, first_name, last_name, facility_id, position, email

<b> Primary Key: </b> `manager_id`

<b> Relations: </b> 
- Has many `employees`
- Belongs to one `facilities`
- Belongs to many `timesheets`
- Has many `approvals`

### `users`
<b>Keys:</b>

    user_id, email, password, employee_id, type

<b> Primary Key: </b> `user_id`

<b> Relations: </b> 
- Has one `employees`

### `timesheets`
<b>Keys:</b>

    timesheet_id, approval_id, start_date, end_date, facility_id, manager_id

<b> Primary Key: </b> `timesheet_id`

<b> Relations: </b> 
- Belongs to one `employees`
- Has  one `managers`
- Belongs to one `facilities`
- Has one `approvals`
- Has many `timesheet_entries`

### `timesheet_entries`
<b>Keys:</b>

    timesheet_entry_id, date, start_time, end_time, rate_type, worked_hours, note, worked_hours

<b> Primary Key: </b> `timesheet_id`

<b> Relations: </b> 
- Belongs to one `timesheets`
- Has many `timesheet_entry_codes`

### `timesheet_entry_codes`
<b>Keys:</b>

    timesheet_entry_code_id, type, hours

<b> Primary Key: </b> `timesheet_entry_code_id`

<b> Relations: </b> 
- Belongs to one `timesheet_entries`

### `approvals`
<b>Keys:</b>

    approval_id, manager_id, date, status, timesheet_id

<b> Primary Key: </b> `approval_id`

<b> Relations: </b> 
- Belongs to one `timesheets`
- Has one `managers`

### `facilities`
<b>Keys:</b>

    facility_id, address, city, zipcode

<b> Primary Key: </b> `facility_id`

<b> Relations: </b> 
- Has many `timesheets`
- Has many `managers`
- Has many `employees`

### `payrates`
<b>Keys:</b>

    payrate_id, role, employee_id, pay, effective_start, effective_end

<b> Primary Key: </b> `payrate_id`

<b> Relations: </b> 
- Has one `employees`
- Belongs to one `facility`

## Prisma Schema
```
model Employees {
  id                    Int           @id @default(autoincrement())
  first_name            String
  last_name             String
  facility              Facilities    @relation(fields: [facility_id], references: [id])
  facility_id           Int 
  position              String        
  manager               Managers      @relation(fields: [manager_id], references: [id]) 
  manager_id
  created_at            DateTime      @default(now())
}

model Managers {
  id                    Int           @id @default(autoincrement())
  first_name            String
  last_name             String
  facility              Facilities    @relation(fields: [facility_id], references: [id])
  facility_id           Int 
  position              String        
  created_at            DateTime      @default(now())
}

model User {
  id            Int           @id @default(autoincrement())
  email         String        @unique
  employee      Employees     @relation(fields: [employee_id], references: [id])
  employee_id   Int
  type          String
  password      String
}

model Timesheets {
  id            Int           @id @default(autoincrement())
  
}

model Reservation {
  id          Int       @id @default(autoincrement())
  equipment   Equipment @relation(fields: [equipmentId], references: [id])
  equipmentId Int
  user        User      @relation(fields: [userId], references: [id])
  userId      Int
  status      String    @default("Pending")
  start       DateTime
  end         DateTime
  createdAt   DateTime  @default(now())
}
```
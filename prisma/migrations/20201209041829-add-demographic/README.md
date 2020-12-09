# Migration `20201209041829-add-demographic`

This migration has been generated by Devinda Senanayaka at 12/8/2020, 10:18:29 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "Student" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "studentID" INTEGER NOT NULL,
    "gender" TEXT NOT NULL,
    "dob" TEXT NOT NULL,
    "birthCountry" TEXT NOT NULL,
    "birthState" TEXT NOT NULL,
    "maritialStatus" TEXT NOT NULL,
    "militaryStatus" TEXT NOT NULL,
    "citizenshipStatus" TEXT NOT NULL,
    "citizenshipCountry" TEXT NOT NULL,
    "visaType" TEXT NOT NULL,
    "visaPermit" TEXT NOT NULL,
    "visaCountry" TEXT NOT NULL,
    "advisorId" INTEGER,

    FOREIGN KEY ("advisorId") REFERENCES "Teacher"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Name" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "name" TEXT NOT NULL,
    "type" TEXT NOT NULL,
    "studentId" INTEGER,

    FOREIGN KEY ("studentId") REFERENCES "Student"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Address" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "type" TEXT NOT NULL,
    "addresses" TEXT NOT NULL,
    "studentId" INTEGER,

    FOREIGN KEY ("studentId") REFERENCES "Student"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "PhoneNumber" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "type" TEXT NOT NULL,
    "number" TEXT NOT NULL,
    "ext" TEXT NOT NULL,
    "country" INTEGER NOT NULL,
    "preferred" BOOLEAN NOT NULL,
    "studentId" INTEGER,

    FOREIGN KEY ("studentId") REFERENCES "Student"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "EmailAddress" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "type" TEXT NOT NULL,
    "address" TEXT NOT NULL,
    "preferred" BOOLEAN NOT NULL,
    "studentId" INTEGER,

    FOREIGN KEY ("studentId") REFERENCES "Student"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Contact" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "primary" BOOLEAN NOT NULL,
    "name" TEXT NOT NULL,
    "relationship" TEXT NOT NULL,
    "number" TEXT NOT NULL,
    "ext" INTEGER NOT NULL,
    "country" TEXT NOT NULL,
    "studentId" INTEGER,

    FOREIGN KEY ("studentId") REFERENCES "Student"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Class" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "title" TEXT,
    "detail" TEXT,
    "startDate" DATETIME,
    "endDate" DATETIME,
    "startTime" INTEGER,
    "endTime" INTEGER,
    "teacherId" INTEGER,

    FOREIGN KEY ("teacherId") REFERENCES "Teacher"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

CREATE TABLE "Teacher" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "email" TEXT NOT NULL,
    "name" TEXT NOT NULL
)

CREATE TABLE "_RegistrationRecord" (
    "A" INTEGER NOT NULL,
    "B" INTEGER NOT NULL,

    FOREIGN KEY ("A") REFERENCES "Class"("id") ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY ("B") REFERENCES "Student"("id") ON DELETE CASCADE ON UPDATE CASCADE
)

CREATE UNIQUE INDEX "_RegistrationRecord_AB_unique" ON "_RegistrationRecord"("A", "B")

CREATE INDEX "_RegistrationRecord_B_index" ON "_RegistrationRecord"("B")
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201209041829-add-demographic
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,88 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "sqlite"
+  url = "***"
+}
+
+model Student {
+  id                      Int     @id @default(autoincrement())
+  studentID               Int
+  gender                  String
+  dob                     String
+  birthCountry            String
+  birthState              String
+  maritialStatus          String
+  militaryStatus          String
+  citizenshipStatus       String
+  citizenshipCountry      String
+  visaType                String
+  visaPermit              String
+  visaCountry             String
+  names                   Name[]
+  addresses               Address[]
+  phonenumber             PhoneNumber[]
+  emails                  EmailAddress[]
+  contacts                Contact[]
+
+  advisor   Teacher? @relation(fields: [advisorId], references: [id])
+  advisorId Int?
+  classes   Class[] @relation("RegistrationRecord")
+}
+
+model Name{
+    id  Int @id
+  name  String
+  type  String
+}
+model Address{
+    id      Int @id
+  type      String
+  addresses String
+}
+model PhoneNumber{
+  id        Int @id
+  type      String
+  number    String
+  ext       String
+  country   Int
+  preferred Boolean
+}
+model EmailAddress{
+  id        Int @id
+  type      String
+  address   String
+  preferred Boolean
+}
+model Contact{
+    id              Int @id
+    primary         Boolean
+    name            String
+    relationship    String
+    number          String
+    ext             Int
+    country         String
+}
+
+model Class {
+  id        Int       @id @default(autoincrement())
+  title     String?
+  detail    String?
+  startDate DateTime?
+  endDate   DateTime?
+  startTime Int?
+  endTime   Int?
+  teacherId Int?
+  teacher   Teacher?  @relation(fields: [teacherId], references: [id])
+  students  Student[] @relation("RegistrationRecord")
+}
+
+model Teacher {
+  id      Int       @id @default(autoincrement())
+  email   String
+  name    String
+  Class   Class[]
+  Student Student[]
+}
```


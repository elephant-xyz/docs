
# 🧭 Property Schema Standardization: Residential + Commercial

## ❗ Problem

Real estate data often suffers from a lack of separation between:

- 🏗 **Physical structure** (e.g. duplex, apartment, office)
- 🧾 **Ownership type** (e.g. fee simple, condo, leasehold)
- 🏘 **Governance** (e.g. HOA or condo association)
- 🏢 **Use/zoning** (residential vs. commercial vs. mixed)

This causes:
- Broken filters (e.g., "not a condo" returning townhomes that are legally condos)
- Legal misrepresentation
- Confusion in AI models, valuations, MLS ingest, or user-facing apps

---

## 🎯 Our Schema Goals

- Separate **what something is** (structure) from **how it’s owned** and **what it’s used for**
- Support both **residential and commercial** property types
- Be extensible, machine-readable, and compatible with county, MLS, and MISMO data

---

## ✅ Updated Schema Overview

### 🏗 `structureType` (in `property` class)

**What the building physically looks like.**

```ts
enum structureType {
  // Residential
  singleFamilyDetached,
  singleFamilyAttached,
  multiFamily2To4,
  multiFamily5Plus,
  manufacturedHome,

  // Commercial
  commercialRetail,
  commercialOffice,
  industrial,
  warehouse,
  flexSpace,
  hotelOrHospitality,
  mixedUseBuilding,
  specialPurposeBuilding,
  vacantCommercialLand
}
```

---

### 🧾 `ownershipType` (in `sales_history` class)

**How the property is legally owned.**

```ts
enum ownershipType {
  feeSimple,
  condominium,
  cooperative,
  groundLease,

  // Optional additions
  commercialCondominium,
  leasehold,
  tripleNetLease
}
```

---

### 🧱 `useType` (in `property` class)

**What the property is zoned or used for.**

```ts
enum useType {
  residential,
  commercial,
  industrial,
  mixedUse,
  agricultural,
  specialPurpose,
  vacant
}
```

---

### 🏘 `homeowners_association` (standalone class)

```json
{
  "homeowners_association_name": "Jupiter Shores HOA",
  "homeowners_association_type": "Mandatory",
  "homeowners_association_amount": 325.00,
  "homeowners_association_fee_frequency": "Monthly",
  "amenities": ["pool", "clubhouse"]
}
```

---

## 📦 Sample Object for a Commercial Condo Office Unit

```json
{
  "structureType": "commercialOffice",
  "useType": "commercial",
  "ownershipType": "commercialCondominium",
  "zoning": "C-1A",
  "homeownersAssociation": {
    "homeowners_association_name": "Downtown Business Park Assoc",
    "homeowners_association_type": "Condominium",
    "homeowners_association_fee_frequency": "Monthly",
    "homeowners_association_amount": 600.00
  }
}
```

---

## 🔄 Mapping to Real-World County Classifications

| County Field             | Maps to Field     | Notes                             |
|--------------------------|------------------|-----------------------------------|
| Appraiser Property Use   | `useType`         | Zoning/usage intent               |
| Building Type            | `structureType`   | May need mapping from raw strings|
| Deed Type / Title        | `ownershipType`   | Recorded legal ownership          |
| HOA/Condo Data           | `homeowners_association` | Fee, name, governance        |

---

## 🧠 Summary

| Layer      | Class                   | Field           | Purpose                                  |
|------------|-------------------------|------------------|-------------------------------------------|
| Structure  | `property`              | `structureType`   | What the building physically is          |
| Usage      | `property`              | `useType`         | Zoning/intended purpose                  |
| Ownership  | `sales_history`         | `ownershipType`   | Legal form of title/deed                |
| Governance | `homeowners_association`| (various)         | Rules, fees, common area control        |

---

## ✅ Benefits of This Model

- Clear modeling of residential + commercial
- Better filtering, pricing, and AI reasoning
- Compatible with zoning, MLS, county appraisal, and mortgage data
- Scalable across edge cases (e.g. mixed-use, condos, duplexes)

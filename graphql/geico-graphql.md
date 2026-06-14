# GEICO Insurance GraphQL Schema

## Overview

GEICO (Government Employees Insurance Company) is the second-largest private passenger auto insurer in the United States, operating as a subsidiary of Berkshire Hathaway. GEICO serves customers directly via geico.com, the GEICO Mobile app, and call centers. It does not publish a self-service public developer API; agent and partner integrations (comparative raters, dealer integrations) are handled through private B2B agreements.

This conceptual GraphQL schema models the domain objects and operations that would underlie GEICO's quoting, policy management, claims, and discount systems based on publicly documented product lines and features.

## Product Lines Modeled

- Auto Insurance (personal passenger vehicles)
- Home Insurance (homeowners and renters)
- Renters Insurance
- Motorcycle Insurance
- Boat / Watercraft Insurance
- Life Insurance
- Business / Commercial Auto Insurance

## Key Domain Areas

### Policy Management

Policies are the central resource. Each policy belongs to a `PolicyHolder` and may have one or more `NamedInsured` parties, `Vehicle` records (for auto), and a set of `Coverage` objects. Policies carry a `Premium` that is derived from rating factors including vehicle details, driver history, location, and applicable discounts.

### Quoting

A `RateQuote` captures a point-in-time pricing estimate. Quotes are parameterized by vehicle information (`VehicleDetails`, `VIN`, `AutoYear`, `AutoMake`, `AutoModel`), coverage selections (`BodilyInjury`, `PropertyDamage`, `Collision`, `Comprehensive`, etc.), and driver records (`MVRRecord`, `AccidentRecord`, `ViolationRecord`).

### Discounts

GEICO is known for a wide range of discounts. The schema captures the discount hierarchy through a base `GeicoDiscount` interface and concrete types: `MilitaryDiscount`, `FederalDiscount`, `GoodDriverDiscount`, `MultiPolicy`, `MultiVehicle`, `MatureDriver`, `NewVehicle`, `GoodStudent`.

### DriveEasy Telematics

The `DriveEasy` program tracks driving behavior via the GEICO Mobile app. Individual trips are represented as `DriveEasyTrip` records which feed into an aggregate `DriveScore` that influences premium adjustments.

### Claims

A `Claim` progresses through a lifecycle captured by `ClaimStatus`. Claims may involve a `ClaimAdjuster`, one or more `RepairShop` references, a damage `Estimate`, `ClaimPayment` records, and optionally a `RentalCar` arrangement.

### Agent Locator

The `Agent` and `AgentLocator` types support local agent lookup for customers who prefer in-person service.

### Authentication

`APIKey` and `Token` represent the credential objects used in B2B partner integrations.

## GraphQL Operations

### Queries

| Query | Description |
|---|---|
| `policy(id: ID!)` | Fetch a single policy by ID |
| `policies(holderId: ID!)` | List all policies for a policyholder |
| `policyHolder(id: ID!)` | Fetch policyholder details |
| `vehicle(vin: String!)` | Look up a vehicle by VIN |
| `rateQuote(input: RateQuoteInput!)` | Request a real-time rate quote |
| `claim(id: ID!)` | Fetch a single claim |
| `claims(policyId: ID!)` | List claims for a policy |
| `discounts(policyId: ID!)` | List discounts applied to a policy |
| `driveScore(holderId: ID!)` | Fetch the current DriveEasy score |
| `agent(id: ID!)` | Fetch an agent record |
| `agentLocator(zip: String!)` | Find local agents by ZIP code |

### Mutations

| Mutation | Description |
|---|---|
| `createQuote(input: RateQuoteInput!)` | Initiate a new rate quote |
| `bindPolicy(quoteId: ID!)` | Convert an accepted quote to a policy |
| `addVehicle(policyId: ID!, vehicle: VehicleInput!)` | Add a vehicle to an existing auto policy |
| `removeVehicle(policyId: ID!, vehicleId: ID!)` | Remove a vehicle |
| `addDriver(policyId: ID!, driver: DriverInput!)` | Add a named insured/driver |
| `updateCoverage(policyId: ID!, coverage: CoverageInput!)` | Adjust coverage selections |
| `fileClaim(policyId: ID!, input: ClaimInput!)` | File a new claim |
| `updateClaim(claimId: ID!, input: ClaimUpdateInput!)` | Update claim details |
| `enrollDriveEasy(policyId: ID!)` | Enroll a policy in the DriveEasy telematics program |
| `applyDiscount(policyId: ID!, discount: DiscountInput!)` | Apply a qualifying discount |
| `issueToken(apiKey: String!)` | Exchange an API key for a session token |

## Schema Source

This schema is conceptual, derived from GEICO's public product documentation, mobile app feature descriptions, and industry-standard insurance data models. It does not represent an officially published GEICO API.

## References

- GEICO Auto Insurance: https://www.geico.com/auto-insurance/
- GEICO Claims: https://www.geico.com/claims/
- GEICO Mobile App: https://www.geico.com/mobile/
- GEICO About: https://www.geico.com/about/
- GitHub Organization: https://github.com/GEICO

# Data Model

The model follows a Star Schema design:

Fact table:
- FactSales

Dimensions:
- DimDate
- DimTime
- DimProduct
- DimPayment

Relationships:
- One-to-many from each dimension to FactSales  
- Single direction filtering  
- Optimized for performance and analytical clarity

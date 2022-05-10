## About Our Business and Our Products

_At Mark Cuban Cost Plus Drug Company, we offer hundreds of common (and often life-saving) medications at the lowest possible prices._

Our transparent prices have four components:

- **Manufacturing**

  Most generic drugs are very inexpensive to produce. For example, a 30 count supply of 400mg Imatinib costs just $38.40. We do our best to price our drugs as close to the manufacturer’s price as possible.

- **15% Markup**

  We have a small team working hard to negotiate lower drug prices and add more products to our pharmacy every day. We ask just **15% more than the manufacturer’s price** to support our operations. All other aspects of our price are passed on with zero markup.

- **Pharmacy Labor**

  Our pharmacy needs to keep the lights on, too. We pass on the true cost of our pharmacists’ time and materials with zero markup. **This cost is always $3.00 per drug per order**, so you will maximize your savings by ordering the largest quantity possible each time you checkout.

- **Shipping**

  This is the amount we spend to package up your prescription and mail it with a shipping carrier. For 400mg Imatinib, this includes the pill bottle and envelope for shipping, which costs $0.48, and a variable fee for carriers, depending on where we are shipping to.

## API Usage and Samples

Cost Plus Drugs provides an API, with results served from GCP US-Central-1 at the following URL:

[https://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main](https://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main)

### Full Listings

By default, calls to the `main` entrypoint will return a full set of all medications available. For example,

```bash
curl "https://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main"
```

returns:

```json
{
  "results": [
    {
      "brand_name": "",
      "form": "Tablet",
      "medication_name": "Abacavir / Lamivudine",
      "ndc": "68180028806",
      "pill_nonpill": "Pill",
      "slug": "AbacavirLamivudine-600mg_300mg-Tablet",
      "strength": "600mg/300mg",
      "unit_price": "$1.820",
      "url": "https://costplusdrugs.com/medications/abacavirlamivudine-600mg_300mg-tablet/"
    },
    {
      "brand_name": "",
      "form": "Tablet",
      "medication_name": "Albendazole",
      "ndc": "78482011002",
      "pill_nonpill": "Pill",
      "slug": "Albendazole-200mg-Tablet",
      "strength": "200mg",
      "unit_price": "$17.250",
      "url": "https://costplusdrugs.com/medications/albendazole-200mg-tablet/"
    },
  ...

```

Canonical purchase information is available at the returned `url` for each medication.

A full example output, with offerings and prices as of 2022-03-23, is available [here](output-ex-001.json).

### Search and query filters

In addition to the full listings, the following query string parameters may be used to search for specific medications
or sets of medications:

- `ndc` (National Drug Code, without hyphens)
- `brand_name` (Brand name, case insensitive)
- `medication_name` (Medication name, case insensitive)
- `strength` (Medication dosage strength. Caller must specify value and unit, as in `10mg`.)

For example,

```bash
curl "http://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?ndc=16729017117"
```

finds the corresponding NDC:

```json
{
  "results": [
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017117",
      "pill_nonpill": "Pill",
      "slug": "Amitriptyline-10mg-Tablet",
      "strength": "10mg",
      "unit_price": "$0.050",
      "url": "https://costplusdrugs.com/medications/amitriptyline-10mg-tablet/"
    }
  ]
}
```
A related search, by brand name:

```bash
curl "https://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?brand_name=Elavil"
```
returns all available strengths of *Amitriptyline*:

```json
{
  "results": [
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017501",
      "pill_nonpill": "Pill",
      "slug": "Amitriptyline-100mg-Tablet",
      "strength": "100mg",
      "unit_price": "$0.560",
      "url": "https://costplusdrugs.com/medications/amitriptyline-100mg-tablet/"
    },
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017117",
      "pill_nonpill": "Pill",
      "slug": "Amitriptyline-10mg-Tablet",
      "strength": "10mg",
      "unit_price": "$0.050",
      "url": "https://costplusdrugs.com/medications/amitriptyline-10mg-tablet/"
    },
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017601",
      "pill_nonpill": "Pill",
      "slug": "Amitriptyline-150mg-Tablet",
      "strength": "150mg",
      "unit_price": "$0.570",
      "url": "https://costplusdrugs.com/medications/amitriptyline-150mg-tablet/"
    },
  ...
```

Combinations of search parameters are also supported. For example, further specifying a desired dosage,
by adding a `strength` parameter, returns the same individual result as the above `ndc` query:

```bash
curl "https://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?brand_name=Elavil&strength=10mg"
```

### Price and quantity queries

Quotes for specific products at given unit quantities can be requested with the aditional parameter `quantity_units`. Each of:

```bash
curl "http://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?ndc=16729017117&quantity_units=30"
```
and
```bash
curl "http://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?medication_name=amitriptyline&strength=10mg&quantity_units=30"
```
return:
```json
{
  "results": [
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017117",
      "pill_nonpill": "Pill",
      "requested_quote": "$4.50",
      "requested_quote_units": 30,
      "slug": "Amitriptyline-10mg-Tablet",
      "strength": "10mg",
      "unit_price": "$0.050",
      "url": "https://costplusdrugs.com/medications/amitriptyline-10mg-tablet/"
    }
  ]
}

```

Price quotes via search by brand name are also supported, however callers *must* specify that generic equivalents are
acceptable, with the additional query term `generic_equivalent_ok` set to `yes` or `true`. That is,

```bash
curl "http://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?brand_name=elavil&strength=10mg&quantity_units=30"
```
returns the partial error:

```json
{
  "results": [
    {
      "brand_name": "Elavil",
      "error_message": "You must affirm parameter 'generic_equivalent_ok' with 'true' or 'yes' to receive quote",
      "form": "Tablet",
```
To search for generics by equivalent brand, the format is:

```bash
curl "http://us-central1-costplusdrugs-publicapi.cloudfunctions.net/main?brand_name=elavil&strength=10mg&quantity_units=30&generic_equivalent_ok=true"
```
which returns:
```json
{
  "results": [
    {
      "brand_name": "Elavil",
      "form": "Tablet",
      "medication_name": "Amitriptyline",
      "ndc": "16729017117",
      "pill_nonpill": "Pill",
      "requested_quote": "$4.50",
      "requested_quote_units": 30,
      "slug": "Amitriptyline-10mg-Tablet",
      "strength": "10mg",
      "unit_price": "$0.050",
      "url": "https://costplusdrugs.com/medications/amitriptyline-10mg-tablet/"
    }
  ]
}
```


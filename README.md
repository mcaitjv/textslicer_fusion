README: Power BI Report Overview

This Power BI report is designed to dynamically compute and display information based on user inputs in slicers, specifically Brand and Country. The report provides insightful feedback for valid or invalid combinations of filters and calculates relevant metrics such as sales data. It is user-friendly, with measures implemented to guide users on their selections and ensure meaningful analysis.
Report Features
1. Dynamic Slicer Interaction

    The report includes slicers for Brand (from the 'Product' table) and Country (from the Store table).
    Selection of these slicers drives the calculations in measures such as filtered sales, validation messages, and dynamic data checks.
    The slicers help users filter the data to specific combinations of Brand and Country, ensuring focused analysis.

2. Feedback Mechanism for Valid Selections

    The report provides real-time feedback on the validity of slicer selections via measures like Selected Brand, Selected Country, and Message Title.
    This feedback ensures users know when:
        A selection is missing (e.g., no values entered in a slicer).
        Selected values are mismatched (e.g., the Brand and Country combination results in no corresponding sales).
        Valid slices result in successfully filtered data (e.g., a correct Brand and Country combination displays relevant sales results).
    Messages guide users to refine their selections if necessary, ensuring better usability.

3. Sales Analysis

    A dynamic measure calculates the sales amount (Sales[Sales Amount]) based on the selected Brand and Country, providing filtered insights into the report's core metric (sales performance).
    Users can slice and dice sales data to explore the impact of specific product lines and countries on overall sales.

Implemented Measures
1. Selected Brand

This measure dynamically captures the Brand selected in the slicer and provides feedback to the user based on the validity of their input. If no Brand is selected (or multiple Brands are selected), it displays an error message guiding the user to refine their filter.

Logic:

    Returns the selected Brand if one is valid.
    Displays: "Channel: Invalid Entry. Please Retry!" if the slicer selection is missing or invalid.

dax

Selected Brand =
VAR _selected_brand = HASONEFILTER('Product'[Brand])
VAR _brand_result = IF(_selected_brand, SELECTEDVALUE('Product'[Brand]), "Channel: Invalid Entry. Please Retry!")
RETURN _brand_result

2. Selected Country

This measure dynamically captures the Country selected in the slicer and provides feedback to the user based on the validity of their input. If no Country is selected (or multiple Countries are selected), it displays an error message guiding the user to refine their filter.

Logic:

    Returns the selected Country if one is valid.
    Displays: "Channel: Invalid Entry. Please Retry!" if the slicer selection is missing or invalid.

dax

Selected Country =
VAR _selected_country = HASONEFILTER(Store[Country])
VAR _country_result = IF(_selected_country, SELECTEDVALUE(Store[Country]), "Channel: Invalid Entry. Please Retry!")
RETURN _country_result

3. Message Title

This measure provides a tailored status message that validates whether the slicers for Brand and Country have valid, meaningful selections and whether the combination results in associated sales data. The feedback system is essential for guiding users to refine their inputs effectively.

Logic:

    Checks whether both slicers are filtered (ISFILTERED).
    Validates whether sales data exists for the combination (NOT(ISEMPTY(Sales))).
    Returns one of three messages:
        "Both entries are valid. See results below:" – Filters are applied, and corresponding sales exist.
        "Two valid values entered, but the combination is incorrect. Please check brand and country entries" – Both slicers are active, but no sales data corresponds to the filtered context.
        "Please ensure that values are entered for both slicers." – One or both slicers are missing valid selections.

dax

Message Title =
VAR _foundbrand = ISFILTERED('Product'[Brand])
VAR _foundcountry = ISFILTERED(Store[Country])
VAR _bothfound = AND(_foundbrand, _foundcountry)
VAR _hassales = NOT(ISEMPTY(Sales))
VAR _allfound = AND(_bothfound, _hassales)
VAR _return =
SWITCH(
    TRUE(), 
    _allfound, "Both entries are valid. See results below:",
    _bothfound, "Two valid values entered, but the combination is incorrect. Please check brand and country entries",
    "Please ensure that values are entered for both slicers."
)
RETURN _return

4. Sales with Filter

This measure calculates the total sales amount (Sales[Sales Amount]) for the entries filtered by Brand and Country. It dynamically responds to slicer selections and adjusts the reported sales amount accordingly.

Logic:

    Filters 'Product'[Brand] and Store[Country] based on slicer selections using SELECTEDVALUE.
    Returns the sum of sales for filtered entries.

dax

Sales with Filter =
CALCULATE(
    Sales[Sales Amount],
    'Product'[Brand] = SELECTEDVALUE('Product'[Brand]),
    Store[Country] = SELECTEDVALUE(Store[Country])
)

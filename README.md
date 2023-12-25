# ValidationWithList-
Data Validation or data annotations With List

//Make a model for custom errors to add all error through the object


public class CustomError
    {
        public int Index1 { get; set; }
        public int Index2 { get; set; }
        public string FieldName { get; set; }
        public string Message { get; set; }
        public bool isTouched { get; set; }
        public bool hasError { get; set; }
    }



Order SelectedOrder = new Order();
private bool isValidData = false;
private int CurrentIndex = 1;
private int DisplayIndex = 1;
private string GenericErrorMessage = string.Empty;
List<CustomError> customErrors = new List<CustomError>();

    private async Task SaveNewOrder()
    {
        await ValidateData(true);
        if (isValidData)
        {
            await _orderService.SaveNewOrder(NewOrder);
        }
    }

    private async Task ValidateData(bool Submitted)
    {
        try
        {
            //customErrors.Clear();

            isValidData = true;
            int Index1 = 0;
            int Index2 = 0;
            foreach (var WindowsData in SelectedOrder.Windows)
            {
                Index1 = SelectedOrder.Windows.IndexOf(WindowsData) + 1;

                //Validate Property Details
                if (String.IsNullOrEmpty(WindowsData.Name))
                {
                    addErorr(Index1, 0, "Name", "Name is required", (Submitted ? true : null));
                    isValidData = false;
                    ShowGenericValidationMessage();
                }
                else
                {
                    removeErorr(Index1, 0, "Name");
                }

                if (WindowsData.QuantityOfWindows == null || WindowsData.QuantityOfWindows <= 0)
                {
                    addErorr(Index1, 0, "QuantityOfWindows", "Quantity Of Windows should be greater than 0", (Submitted ? true : null));
                    isValidData = false;
                    ShowGenericValidationMessage();
                }
                else
                {
                    removeErorr(Index1, 0, "QuantityOfWindows");
                }

                if (WindowsData.SubElements == null || WindowsData.TotalSubElements <= 0)
                {
                    addErorr(Index1, 0, "TotalSubElements", "Total Sub Elements should be greater than 0", (Submitted ? true : null));
                    isValidData = false;
                    ShowGenericValidationMessage();
                }
                else
                {
                    removeErorr(Index1, 0, "TotalSubElements");
                }

                foreach (var SubElementData in WindowsData.SubElements)
                {
                    Index2 = WindowsData.SubElements.IndexOf(SubElementData) + 1;
                    //IssusingBankId
                    if (SubElementData.Element == null || SubElementData.Element <= 0)
                    {
                        addErorr(Index1, Index2, "Element", "Element should be more than zero", (Submitted ? true : null));
                        isValidData = false;
                        ShowGenericValidationMessage();
                    }
                    else
                    {
                        removeErorr(Index1, Index2, "Element");
                    }

                    if (String.IsNullOrEmpty(SubElementData.Type))
                    {
                        addErorr(Index1, Index2, "Type", "Type is required", (Submitted ? true : null));
                        isValidData = false;
                        ShowGenericValidationMessage();
                    }
                    else
                    {
                        removeErorr(Index1, Index2, "Type");
                    }


                    //Width
                    if (SubElementData.Width == null || SubElementData.Width <= 0)
                    {
                        addErorr(Index1, Index2, "Width", "Width should be more than zero", (Submitted ? true : null));
                        isValidData = false;
                        ShowGenericValidationMessage();
                    }
                    else
                    {
                        removeErorr(Index1, Index2, "Width");
                    }

                    //Height
                    if (SubElementData.Width == null || SubElementData.Height <= 0)
                    {
                        addErorr(Index1, Index2, "Height", "Height should be more than zero", (Submitted ? true : null));
                        isValidData = false;
                        ShowGenericValidationMessage();
                    }
                    else
                    {
                        removeErorr(Index1, Index2, "Height");
                    }
                }
            }
        }
        catch (Exception ex)
        {
            isValidData = false;
        }
    }

    private async Task updateValidator(int Index1, int Index2, string FieldName)
    {
        addErorr(Index1, Index2, FieldName, null, true);
        ValidateData(false);
    }

    private void addErorr(int Index1, int Index2, string FieldName, string Message, bool? isTouched)
    {
        CustomError c = customErrors.Where(w => w.Index1 == Index1 && w.Index2 == Index2 && w.FieldName == FieldName).FirstOrDefault();
        if (c == null)
        {
            c = new CustomError()
                {
                    Index1 = Index1,
                    Index2 = Index2,
                    FieldName = FieldName,
                    isTouched = false
                };
            customErrors.Add(c);
        }
        if (!string.IsNullOrEmpty(Message))
        {
            c.hasError = true;
            c.Message = Message;
        }
        if (isTouched != null)
        {
            c.isTouched = (bool)isTouched;
        }
    }

    private void removeErorr(int Index1, int Index2, string FieldName)
    {
        CustomError c = customErrors.Where(w => w.Index1 == Index1 && w.Index2 == Index2 && w.FieldName == FieldName).FirstOrDefault();
        if (c != null)
        {
            c.hasError = false;
            c.Message = "";
        }
    }

    private string getValidationMsg(int Index1, int Index2, string FieldName)
    {
        CustomError c = customErrors.Where(w => w.Index1 == Index1 && w.Index2 == Index2 && w.FieldName == FieldName).FirstOrDefault();
        if (c != null && c.isTouched == true && c.hasError == true)
        {
            return c.Message;
        }
        return "";
    }

    private void ShowGenericValidationMessage()
    {
        GenericErrorMessage = "* Required information missing";
        StateHasChanged();
    }


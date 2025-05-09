# ViewModel-for-FlyOut
This is a ViewModel and refresh for Menu items in Flyout Menu view.
With this code,  I do not have successful refresh of Menu.


-----AppShell.cs

    public partial class AppShell : Shell
    {
        public AppShell()
        {

            InitializeComponent();
            this.BindingContext = new ShowHideMenu.ShowParametersViewModel();
         }
    }


-----MainPage.cs

  public partial class MainPage  :  ContentPage
  {
  
      public static int AdminLevel { get; internal set; } = 0;

      public MainPage()
        {

        }
    }



----- SignIn.cs

     **  There will be a step of verifying a password when the login button is chosen. 
         The result will be what is the only code in the void currently.
         I have the LogInBttn binded to the Command of 
         ShowParametersCommand
         in the ViewModel.  This will have to be a call from the return of the LogInButton.
         I am not sure how to send the command.

public partial class SignIn : ContentPage
{     

    public SignIn()
	{
	   InitializeComponent();
     this.BindingContext = AppShell.Current.BindingContext;
   }

	public void LogInBtn(object sender, EventArgs args)
	{

           //  this int will be set later.  just hard coded for now.
           MainPage.AdminLevel = 1;
          InvokeCommandFromCodeBehind();
   
  }

    private void InvokeCommandFromCodeBehind()
    {
        if (BindingContext is ShowParametersViewModel viewModel)
        {
            if (viewModel.ShowParametersCommand.CanExecute(null))
            {
                viewModel.ShowParametersCommand.Execute(null);
            }
        }
    }

  

}






-------SignIn.xaml

        <Shell>

        <Button 
               x:Name="PasswSubmitBtn"
               Text="SUBMIT"
               Command="{Binding ShowParametersCommand}"
               Clicked="LogInBtn"
               BackgroundColor="Green"
               HorizontalOptions="Fill" VerticalOptions="Start"
               />

        </Shell>





------ShowHideMenu.cs

  class ShowHideMenu
  
  {
  
      public class ShowParametersViewModel : INotifyPropertyChanged
      {

          private bool _parameters;
          public bool Parameters
          {
              get {

                  return _parameters;

              }
              set
              {

                  _parameters = value;
                 
                  RaisePropertyChanged(nameof(Parameters));

              }

          }

          public ShowParametersViewModel()
          {

              if (MainPage.AdminLevel == 1)
              {
                ShowParametersCommand = new Command(() => Parameters = true);
              }
              else {
                ShowParametersCommand = new Command(() => Parameters = false);
              }


              
          }

 

          public event PropertyChangedEventHandler PropertyChanged;

          public virtual void RaisePropertyChanged(string propertyName)
          {
              
              this.PropertyChanged?.Invoke(this ,
                  new PropertyChangedEventArgs(propertyName));
          }

          

          public ICommand? ShowParametersCommand { get; private set; }

      }

  
  }




----- AppShell xaml

    <Shell>

    <FlyoutItem Title="SET CREDENTIALS" 
            x:Name="manFlyOut"    
             >
        <Tab Title="SET PASSWORDS">
            <ShellContent x:Name="SetPasswForm" ContentTemplate="{DataTemplate local:SetPasswords}" IsVisible="{Binding Parameters}" />
        </Tab>
        <Tab Title="SET CREDENTIALS">
            <ShellContent x:Name="SetCredForm" ContentTemplate="{DataTemplate local:SetCredentials}"  IsVisible="{Binding Parameters}"/>
        </Tab>
        </FlyoutItem>

 </Shell>

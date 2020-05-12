# Django_Custom_Auth
Change the authentication method to mobile number 

Abstarct user class:
```python
class UserManager(BaseUserManager):
    """Define a model manager for User model with no username field."""

    use_in_migrations = True

    def _create_user(self, phone, password, **extra_fields):
        """Create and save a User with the given phone and password."""
        if not phone:
            raise ValueError('The given phone must be set')
        user = self.model(phone=phone, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_user(self, phone, password=None, **extra_fields):
        """Create and save a regular User with the given phone and password."""
        extra_fields.setdefault('is_staff', False)
        extra_fields.setdefault('is_superuser', False)
        return self._create_user(phone, password, **extra_fields)

    def create_superuser(self, phone, password, **extra_fields):
        """Create and save a SuperUser with the given phone and password."""
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        if extra_fields.get('is_staff') is not True:
            raise ValueError('Superuser must have is_staff=True.')
        if extra_fields.get('is_superuser') is not True:
            raise ValueError('Superuser must have is_superuser=True.')

        return self._create_user(phone, password, **extra_fields)
   ```
   
   Extend the class from AbstractUser
```python
class User(AbstractUser):
    username = None
    phone = models.CharField(verbose_name='Mobile Number', max_length=10, unique=True)
    #....
    
    # Use default usermanager
    objects = UserManager()
    USERNAME_FIELD = 'phone'

    def __str__(self):
        return str(self.phone)
 ```
 In settings.py set the auth model
 ```python
 AUTH_USER_MODEL = 'accounts.User'
 ```

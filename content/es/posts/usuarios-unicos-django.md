+++
title = "Nombres de Usuarios Únicos en Django"
date = "2024-01-13T11:19:13-03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Alitux"
authorTwitter = "alituxtdf" #do not include @
cover = ""
tags = ["Django", "Python"]
keywords = ["", ""]
description = "Cómo crear nombres de usuarios autogenerados únicos en Django: Generá un nombre combinando datos del usuario o strings aleatorios y chequeá en la base de datos que no exista. Si ya está tomado, modificalo hasta que sea único antes de guardar el usuario."
showFullContent = false
readingTime = false
hideComments = false
+++

La creación de nombres de usuarios autogenerados en Django no está implementada por defecto. Es por eso que escribí una simple función, que valiéndose de un CustomUser (No uso User por defecto ya que tengo un modelo personalizado que hereda sus propiedades desde AbstractUser) y tomando como entrada el primer nombre genera nombres de usuarios únicos no predecibles. Si bien no evita ataques por fuerza bruta, mitiga el uso de diccionarios con patrones comunes.

```python
def unique_username(first_name):
    """"
    Función para generar username únicos en base al primer nombre
     
    Parameters:
     
    Keyword arguments:
    first_name
    Return: username
    """
    first_name = re.sub(
        r"([^n\u0300-\u036f]|n(?!\u0303(?![\u0300-\u036f])))[\u0300-\u036f]+", r"\1", 
        normalize( "NFD", first_name), 0, re.I
    )
    first_name = normalize( 'NFC', first_name)
    if not CustomUser.objects.filter(username=first_name).exists():
        return first_name.lower()
    while CustomUser.objects.filter(username=first_name).exists():
        first_name = first_name + str(uuid.uuid4())[:4]
    return first_name.lower()
```
La implementación dentro de una vista FormView queda como sigue:
```python
class SocioCreateView(LoginRequiredMixin, AdminORAdministrativoRequiredMixin, FormView):
    """
    Vista Para crear Socios desde una cuenta admin o Administrativa
    """
     
    form_class = NuevoUsuarioForm
    template_name = "home/sections/administrador/socio_create.html"
    success_url = reverse_lazy("admin_socio_list")
 
    def form_valid(self, form):
        ### Creación de Usuario
        user = CustomUser.objects.create(
            first_name=form.cleaned_data['first_name'],
            last_name=form.cleaned_data['last_name'],
            email=form.cleaned_data['email'],
            username=unique_username(form.cleaned_data['first_name']),
            password=make_password(form.cleaned_data['password']),
            dni=form.cleaned_data['dni'],
            genero=form.cleaned_data['genero'],      
            direccion=form.cleaned_data['direccion'],
            ciudad=form.cleaned_data['ciudad'],      
            provincia=form.cleaned_data['provincia'],
            pais=form.cleaned_data['pais'],
            telefono = form.cleaned_data['telefono'],
            fecha_nacimiento=form.cleaned_data['fecha_nacimiento'],
        )
```
Yo la implementé dentro de una vista, pero tranquilamente se podría utilizar en el modelo o como un validador para un Form.
### python-carteblanche
---
https://github.com/neuman/python-carteblanche/

```py
// carteblanche/mixins.py
import carteblanche.base as cb

try:
  MESSAGE_TEMPLATE = settings.MESSAGES_TEMPLATE
except Exception as e:
  MESSAGES_TEMPLATE = 'carteblanche/messages.html'
  
class NounView(SuccessMessageMixin):
  success_message = "That worked!"
  
  def __init__(self, **kwargs):
    super(NounView, self).__init__(**kwargs)
    self.noun = None
    
  def get_view_required_verbs(self, view_name):
    verbs = []
    for v in self.noun.get_verbs():
      if v.required == True:
        if v.view_name == view_name:
          verbs.append(v)
    return verbs
    
  def get_view_required_unavailable_verbs(self, view_name, user):
    verbs = []
    for v in self.get_view_required_verbs(view_name):
      if not v.is_available(user):
        verbs.append(v)
    return verbs
    
  def get_context_data(self, **kwargs):
    print "get_context_data"
    context = super(NounView, self).get_context_data(**kwargs)
    available_verbs = self.noun.get_available_verbs(self.request.user)
    context['available_verbs'] = available_verbs
    context['condition'] = self.noun.conditions.get_available(self.request.user)
    context['noun'] = self.noun
    self.noun.conditions.cache = {}
    return context
  
  def dispatch(self, *args, **kwargs):
    self.noun = self.get_noun(**kwargs)
    view_name = resolve(self.request.path_info).url_name
    denied_messages = []
    for verb in self.get_view_required_unavailable_verbs(view_name, self.request.user):
      denied_messages.append(verb.denied_message)
    if len(denied_messages) > 0:
      for message in denied_messages:
        messages.add_message(self.request, messages.ERROR, message)
      return render_to_response(MESSAGES_TEMPLATE,{"available_verbs":self.noun.get_available_verbs(self.request.user)}, RequestContext(self.request))
      
    return super(NounView, self).dipatch(*args, **kwargs)
    
  def get_success_message(self, cleaned_data):
    view_name = resolve(self, cleaned_data):
    denied_messages = []
    for verb in self.get_view_required_unavailable_verbs(view_name, self.request.user):
      denied_messages.append(verb.success_message)
      
    return ''.join(denied_messages)
    
  class Meta:
    abstract = True
    
class DjangoVerb(cb.Verb):
  view_name = None
  app = None
  visible = True
  
  def get_url(self):
    '''
    '''
    try:
      return reverse(viewname=self.view_name, args=[self.noun.id], current_app=self.app)
    except Exception as e:
    
def availability_login_required(is_available_func):  
  @wraps(is_available_func, assigned=available_attrs(is_available_func))
  def decorator(self, user):
    print user
    if user.is_authenticated():
      print "is_authenticated = True"
      return is_available_func(self, user)
    else:
      print "is_authenticated = False"
      self.denied_message = "You must be logged in to "+self.display_name+"."
      return False
  return decorator
```

```
```

```
```


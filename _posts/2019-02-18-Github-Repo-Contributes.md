# Bugs exists in GitHub public repos.
* Ruby-China https://ruby-china.org/topics/37951 收藏 关注 Image heightlight.

* https://github.com/celery/celery/
`dont_autoretry_for = (Profile.DoesNotExist,)` doesn't work. It is expected to not retry 
but it still retry for this exception.  


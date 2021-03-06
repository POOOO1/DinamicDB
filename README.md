# DinamicDB
 в build.gradle (module:app) добавлен dagger 
 -установить AndroidInjectionModuleв свой прикладной компонент,
  чтобы обеспечить доступность всех привязок, необходимых для этих базовых типов.

Начать с написания @Subcomponent реализующего AndroidInjector<YourActivity>с @Subcomponent.
Factory расширением AndroidInjector.Factory<YourActivity>:

@Subcomponent(modules = ...)
public interface YourActivitySubcomponent extends AndroidInjector<YourActivity> {
 @Subcomponent.Factory
 public interface Factory extends AndroidInjector.Factory<YourActivity> {}
}
После определения подкомпонента, добавьте его в иерархию компонентов, 
определив модуль, который связывает фабрику подкомпонентов, и добавьте его в компонент, который внедряет ваш Application:

@Module(subcomponents = YourActivitySubcomponent.class)
abstract class YourActivityModule {
 @Binds
 @IntoMap
 @ClassKey(YourActivity.class)
 abstract AndroidInjector.Factory<?>
     bindYourActivityInjectorFactory(YourActivitySubcomponent.Factory factory);
}

@Component(modules = {..., YourActivityModule.class})
interface YourApplicationComponent {}
Совет: если у вашего подкомпонента и его фабрики нет других методов или супертипов, 
кроме тех, которые упомянуты в шаге №2, вы можете использовать @ContributesAndroidInjector их для генерации. 
Вместо шагов 2 и 3 добавьте abstract метод модуля, который возвращает вашу активность, 
аннотируйте ее @ContributesAndroid Injector и укажите модули, которые вы хотите установить в подкомпонент. Если субкомпоненту нужны области, примените аннотации области также к методу.

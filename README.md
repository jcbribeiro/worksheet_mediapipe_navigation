# Worksheet - Implementing Navigation in the MediaPipe Tasks Image Classification Android Demo

![1715096605882](images/1715096605882.png)

## Setup

- Install Android Studio:
  https://developer.android.com/studio/install
- Download/clone the "MediaPipe Tasks Image Classification Android Demo":
  https://github.com/googlesamples/mediapipe/tree/main/examples/image_classification/android
- Compile and run the demo app on your physical device (if one is available):
  https://developer.android.com/studio/run/device

## Update the resources and configuration

### Copy the provided images to res/drawable

### Enable data binding on build.gradle

```kotlin
buildFeatures {
    // ...
    dataBinding true
}
```

## Update the Main Activity and create the Navigation Drawer

### Create res/menu/navdrawer_menu.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- This must be the same id as the nav_graph id of the fragment that will be displayed when this item is clicked. -->
    <item
        android:id="@+id/rulesFragment"
        android:title="Rules" />
</menu>
```

### * [ ] Create the nav_header.xml layout file

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/navHeader"
    android:layout_width="match_parent"
    android:layout_height="192dp"
    android:padding="16dp">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:srcCompat="@drawable/image3" />
</androidx.constraintlayout.widget.ConstraintLayout>
```


### Replace res/layout/activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <androidx.drawerlayout.widget.DrawerLayout
        android:id="@+id/drawerLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                />

            <fragment
                android:id="@+id/fragment_container"
                android:name="androidx.navigation.fragment.NavHostFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:navGraph="@navigation/nav_graph"
                app:defaultNavHost="true"
                />
        </LinearLayout>

        <com.google.android.material.navigation.NavigationView
            android:id="@+id/navView"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_header"
            app:menu="@menu/navdrawer_menu" />

    </androidx.drawerlayout.widget.DrawerLayout>
</layout>
```

### Update MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {
  private lateinit var activityMainBinding: ActivityMainBinding
  private val viewModel: MainViewModel by viewModels()
  private lateinit var drawerLayout: DrawerLayout

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    activityMainBinding = ActivityMainBinding.inflate(layoutInflater)
    setContentView(activityMainBinding.root)

    setSupportActionBar(findViewById(R.id.toolbar))
    drawerLayout = activityMainBinding.drawerLayout
    val navController = this.findNavController(R.id.fragment_container)
    NavigationUI.setupActionBarWithNavController(this, navController, drawerLayout)
    NavigationUI.setupWithNavController(activityMainBinding.navView, navController)
  }

  override fun onSupportNavigateUp(): Boolean {
    val navController = this.findNavController(R.id.fragment_container)
    return NavigationUI.navigateUp(navController, drawerLayout) || super.onSupportNavigateUp()
  }

  override fun onBackPressed() {
    finish()
  }
}
```

## Update camera_fragment.xml

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/camera_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <RelativeLayout
        android:id="@+id/game_info"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/label"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="5dp"
            android:text="Find 5 objects!"
            android:textSize="11sp" />

        <ProgressBar
            android:id="@+id/progressBar"
            style="?android:attr/progressBarStyleHorizontal"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="5dp"
            android:progress="30"
            android:layout_below="@id/label"
            />

        <androidx.camera.view.PreviewView
            android:id="@+id/view_finder"
            android:layout_width="match_parent"
            android:layout_height="180dp"
            android:layout_below="@id/progressBar"
            android:layout_alignParentBottom="true"
            />
    </RelativeLayout>

    <include
        android:id="@+id/bottom_sheet_layout"
        layout="@layout/info_bottom_sheet" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerview_results"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="top"
        android:layout_marginBottom="@dimen/bottom_sheet_peek_height"
        android:background="@color/bottom_sheet_background"
        android:clipToPadding="true"
        android:padding="@dimen/bottom_sheet_padding"
        app:layout_anchor="@id/bottom_sheet_layout"
        app:layout_anchorGravity="center"
        app:layout_behavior="com.google.android.material.appbar.AppBarLayout$ScrollingViewBehavior" />

    <View
        android:layout_width="wrap_content"
        android:layout_height="1dp"
        android:background="@color/mp_color_primary"
        app:layout_anchor="@id/recyclerview_results"
        app:layout_anchorGravity="bottom" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

## Update the View Model

### Update MainViewModel.kt

```kotlin
class MainViewModel : ViewModel() {
    val labelLiveData = MutableLiveData<List<String>>()

    private var _gamesCompleted = 0
    private var _gamesWon = 0

    val gamesCompleted: Int
        get() = _gamesCompleted
    val gamesWon: Int
        get() = _gamesWon

    fun onGameFinished(won: Boolean) {
        _gamesCompleted++
        if (won) {
            _gamesWon++
        }
    }

    // ...
```

## Create the necessary Screens

### Create the fragments

- TitleFragment
- GameWonFragment
- GameLostFragment
- Rules Fragment (the .kt file is not needed, you may delete it).

### RulesFragment: layout/fragment_rules.xml

```xml
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <ImageView
            android:id="@+id/rulesImage"
            android:layout_width="0dp"
            android:layout_height="240dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:srcCompat="@drawable/image1" />

        <TextView
            android:id="@+id/rulesText"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:padding="16dp"
            android:text="The goal of this game is simple: find 5 elements -- any elements -- in under to win! Just make sure you do it in under 30 seconds...! Good luck!"
            android:textAppearance="@style/TextAppearance.AppCompat.Large"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/rulesImage" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</ScrollView>
```

### TitleFragment: layout/fragment_title.xml

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:gravity="center"
        >

        <ImageView
            android:id="@+id/titleImage"
            android:layout_width="240dp"
            android:layout_height="240dp"
            app:srcCompat="@drawable/image3" />

        <Button
            android:id="@+id/playButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Play"
            android:textStyle="bold"
            android:padding="16dp" />
            />

        <TextView
            android:id="@+id/scoreText"
            tools:text="Score"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textStyle="bold"
            android:textSize="18dp" />
    </LinearLayout>
</layout>
```

### TitleFragment: TitleFragment.kt

```kotlin
class TitleFragment : Fragment() {
  private val viewModel: MainViewModel by activityViewModels()
  private lateinit var binding: FragmentTitleBinding

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                            savedInstanceState: Bundle?): View {
    binding = DataBindingUtil.inflate(inflater,
      R.layout.fragment_title,container,false)

    binding.playButton.setOnClickListener {
      it.findNavController().navigate(TitleFragmentDirections.actionTitleFragmentToCameraFragment())
    }

    setHasOptionsMenu(true)
    return binding.root
  }

  override fun onCreateOptionsMenu(menu: Menu, inflater: MenuInflater) {
    super.onCreateOptionsMenu(menu, inflater)
    inflater.inflate(R.menu.navdrawer_menu,menu)
  }

  override fun onOptionsItemSelected(item: MenuItem): Boolean {
    return NavigationUI.onNavDestinationSelected(item, requireView().findNavController())
        || super.onOptionsItemSelected(item)
  }

  override fun onResume() {
    super.onResume()

    binding.scoreText.text = viewModel.toString()
  }
}
```

### GameWonFragment: fragment_game_won.xml

```xml
<?xml version="1.0" encoding="utf-8"?>

<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    tools:context=".GameWonFragment">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/gameWonConstraintLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <Button
            android:id="@+id/nextMatchButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Next Match"
            android:textStyle="bold"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/youWinImage" />

        <ImageView
            android:id="@+id/youWinImage"
            android:layout_width="wrap_content"
            android:layout_height="240dp"
            app:layout_constraintBottom_toTopOf="@id/nextMatchButton"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_chainStyle="spread"
            app:srcCompat="@drawable/image11" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

### GameWonFragment: GameWonFragment.kt

```kotlin
class GameWonFragment : Fragment() {
  private val viewModel: MainViewModel by activityViewModels()

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                            savedInstanceState: Bundle?): View {
    val binding: FragmentGameWonBinding = DataBindingUtil.inflate(
      inflater, R.layout.fragment_game_won, container, false)

    binding.nextMatchButton.setOnClickListener { view: View ->
      view.findNavController().navigate(GameWonFragmentDirections.actionGameWonFragmentToTitleFragment())
    }

    viewModel.onGameFinished(won = true)

    return binding.root
  }
}
```

### GameOverFragment: fragment_game_over.xml

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    tools:context=".GameOverFragment">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/gameOverConstraintLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <ImageView
            android:id="@+id/tryAgainImage"
            android:layout_width="wrap_content"
            android:layout_height="240dp"
            app:layout_constraintBottom_toTopOf="@id/tryAgainButton"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_chainStyle="spread"
            app:srcCompat="@drawable/image7" />

        <Button
            android:id="@+id/tryAgainButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginBottom="8dp"
            android:text="Try Again"
            android:textStyle="bold"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tryAgainImage" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

### GameOverFragment: GameOverFragment.kt

```kotlin
class GameOverFragment : Fragment() {
  private val viewModel: MainViewModel by activityViewModels()

  override fun onCreateView(
    inflater: LayoutInflater, container: ViewGroup?,
    savedInstanceState: Bundle?
  ): View? {
    // Inflate the layout for this fragment
    val binding: FragmentGameOverBinding = DataBindingUtil.inflate(
      inflater, R.layout.fragment_game_over, container, false)

    binding.tryAgainButton.setOnClickListener { view: View ->
      view.findNavController().navigate(GameOverFragmentDirections.actionGameOverFragmentToTitleFragment())
    }
    viewModel.onGameFinished(won = false)

    return binding.root
  }
}
```

## Update the Nabigation Graph

### Update nav_graph.xml

- add the following fragments and actions to the component tree of the navigation graph:

![1715096659117](images/1715096659117.png)

![1715096605882](images/1715096605882.png)

```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/titleFragment">

    <fragment
        android:id="@+id/permissions_fragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.PermissionsFragment"
        android:label="Permissions"
        >

        <action
            android:id="@+id/action_permissions_to_camera"
            app:destination="@id/camera_fragment"
            app:popUpTo="@id/permissions_fragment"
            app:popUpToInclusive="false" />

    </fragment>

    <fragment
        android:id="@+id/camera_fragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.CameraFragment"
        android:label="Play the game!"
        tools:layout="@layout/fragment_camera"
        >
        <action
            android:id="@+id/action_camera_to_permissions"
            app:destination="@id/permissions_fragment"
            app:popUpTo="@id/titleFragment"
            app:popUpToInclusive="true" />
        <action
            android:id="@+id/action_camera_fragment_to_gameWonFragment"
            app:destination="@id/gameWonFragment"
            app:popUpTo="@id/titleFragment"
            app:popUpToInclusive="true"
            />
        <action
            android:id="@+id/action_camera_fragment_to_gameOverFragment"
            app:destination="@id/gameOverFragment"
            app:popUpTo="@id/titleFragment"
            app:popUpToInclusive="true"
            />
    </fragment>
    <fragment
        android:id="@+id/gallery_fragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.GalleryFragment"
        android:label="Gallery" />
    <fragment
        android:id="@+id/titleFragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.TitleFragment"
        android:label="Welcome!"
        tools:layout="@layout/fragment_title"
        >
        <action
            android:id="@+id/action_titleFragment_to_camera_fragment"
            app:destination="@id/camera_fragment" />
    </fragment>
    <fragment
        android:id="@+id/gameWonFragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.GameWonFragment"
        android:label="Game Won!"
        app:popUpTo="@id/titleFragment"
        app:popUpToInclusive="true"
        tools:layout="@layout/fragment_game_won"
        >
        <action
            android:id="@+id/action_gameWonFragment_to_titleFragment"
            app:destination="@id/titleFragment" />
    </fragment>
    <fragment
        android:id="@+id/gameOverFragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.GameOverFragment"
        android:label="Game Over..."
        app:popUpTo="@id/titleFragment"
        app:popUpToInclusive="true"
        tools:layout="@layout/fragment_game_over"
        >
        <action
            android:id="@+id/action_gameOverFragment_to_titleFragment"
            app:destination="@id/titleFragment" />
    </fragment>
    <fragment
        android:id="@+id/rulesFragment"
        android:name="com.google.mediapipe.examples.imageclassification.fragments.RulesFragment"
        android:label="RulesFragment"
        tools:layout="@layout/fragment_rules"
        />
</navigation>

```

## Implement the game's logic

### Update CameraFragment.kt

```kotlin
class CameraFragment : Fragment(), ImageClassifierHelper.ClassifierListener {

  // ...

  override fun onResume() {
    // ...

    gameLogic()
  }

  override fun onCreateView(
    inflater: LayoutInflater,
    container: ViewGroup?,
    savedInstanceState: Bundle?
  ): View {
    // ...

    (activity as AppCompatActivity?)?.supportActionBar?.setDisplayHomeAsUpEnabled(false)

    return fragmentCameraBinding.root
  }

  @SuppressLint("NotifyDataSetChanged")
  override fun onResults(
    resultBundle: ImageClassifierHelper.ResultBundle
  ) {

    for (result in resultBundle.results) {
      for (classification in result.classificationResult().classifications()) {
        if (classification.categories().isNotEmpty()) {
            val newLabel = classification.categories().first().categoryName()
            val currentList = viewModel.labelLiveData.value ?: emptyList()
            if (newLabel !in currentList) {
              val updatedList = currentList + newLabel
              viewModel.labelLiveData.postValue(updatedList)
          }
        }
      }
    }

    // ...
  }

  private fun gameLogic() {
    val totalTime = 30000L
    var elapsedTime = 0L
    var playing = false;

    val countDownTimer = object : CountDownTimer(30000, 1000) {
      override fun onTick(millisUntilFinished: Long) {
        val progress = ((totalTime - millisUntilFinished) / (totalTime / 100)).toInt()
        fragmentCameraBinding.progressBar.progress = progress
        elapsedTime = totalTime - millisUntilFinished
      }

      override fun onFinish() {
        Navigation.findNavController(
          requireActivity(), R.id.fragment_container
        ).navigate(CameraFragmentDirections.actionCameraFragmentToGameOverFragment())
      }
    }

    viewModel.labelLiveData.observe(this, Observer {
      Snackbar.make(fragmentCameraBinding.root, it.last(), Snackbar.LENGTH_LONG).show()

      if (playing)
        fragmentCameraBinding.label.text = it.size.toString() + ": " + it.toString()

      if (it.size >= 5 && playing) {
        countDownTimer.cancel()
        playing = false

        // navigate to game won fragment
        Navigation.findNavController(
          requireActivity(), R.id.fragment_container
        ).navigate(CameraFragmentDirections.actionCameraFragmentToGameWonFragment())
      }
    })

    AlertDialog.Builder(this@CameraFragment.activity)
      .setTitle("Play the Game!")
      .setMessage("Press the Start button to start the game.")
      .setPositiveButton("Start") { dialog, _ ->
        dialog.dismiss()
        playing = true
        countDownTimer.start()
      }
      .setCancelable(false)
      .show()
  }
}
```

Also, replace the former with the latter everywhere in your file:

```kotlin
imageClassifierHelper.currentModel = position
updateControlsUi()
```

should be replaced with

````kotlin
if (::imageClassifierHelper.isInitialized) {
  imageClassifierHelper.currentModel = position
  updateControlsUi()
}
````

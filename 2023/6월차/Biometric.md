**Biometric**
================
안드로이드에서 생체인증을 사용할 수 있었던 것은 API레벨 23(안드로이드6.0) 부터이다.   

이때는 fingerprintManager라는 것을 사용했다.   

하지만 API레벨 28(안드로이드9.0) 부터는 지문이 아닌 다른 방식이 생겨나면서 deprecated 되었다.   
이후부터는 BiometricPrompt를 사용한다.
# **How To Use**
## build.gradle(:app)
dependencies에 implementation 'androidx.biometric:biometric:1.1.0' 을 추가한다.   
## AndroidManifest.xml
manifest에 `<uses-permission android:name="android.permission.USE_BIOMETRIC"/>` 권한을 추가한다.
## BiometricActivity.kt

```kt
class BiometricActivity: AppCompatActivity() {

    companion object {
        const val TAG: String = "BiometricActivity"
    }

    lateinit var binding: ActivityBiometricBinding

    private var executor: Executor? = null
    private var biometricPrompt: BiometricPrompt? = null
    private var promptInfo: BiometricPrompt.PromptInfo? = null

    private val loginLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
            Log.d(TAG, "registerForActivityResult - result : $result")

            if (result.resultCode == Activity.RESULT_OK) {
                Log.d(TAG, "registerForActivityResult - RESULT_OK")
                authenticateToEncrypt()  //생체 인증 가능 여부확인 다시 호출
            } else {
                Log.d(TAG, "registerForActivityResult - NOT RESULT_OK")
            }
        }

    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

//        setContentView(R.layout.activity_biometric)
        binding = ActivityBiometricBinding.inflate(layoutInflater)
        setContentView(binding.root)

        biometricPrompt = setBiometricPrompt()
        promptInfo = setPromptInfo()

        //지문 인증 호출 버튼 클릭 시
        binding.btnBioSet.setOnClickListener {
            authenticateToEncrypt()  //생체 인증 가능 여부확인
        }

    }
    
    
    private fun setPromptInfo(): BiometricPrompt.PromptInfo {

        val promptBuilder: BiometricPrompt.PromptInfo.Builder = BiometricPrompt.PromptInfo.Builder()

        promptBuilder.setTitle("Biometric login for my app")
        promptBuilder.setSubtitle("Log in using your biometric credential")
        promptBuilder.setNegativeButtonText("Use account password")

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) { //  안면인식 api사용 android 11부터 지원
            promptBuilder.setAllowedAuthenticators(BiometricManager.Authenticators.BIOMETRIC_STRONG or BiometricManager.Authenticators.DEVICE_CREDENTIAL)
        }

        promptInfo = promptBuilder.build()
        return promptInfo as PromptInfo
    }


    private fun setBiometricPrompt(): BiometricPrompt {
        executor = ContextCompat.getMainExecutor(this)

        biometricPrompt = BiometricPrompt(this@BiometricActivity, executor!!, object : BiometricPrompt.AuthenticationCallback() {

            override fun onAuthenticationError(errorCode: Int, errString: CharSequence) {
                super.onAuthenticationError(errorCode, errString)
                Toast.makeText(this@BiometricActivity, """"지문 인식 ERROR [ errorCode: $errorCode, errString: $errString ]""".trimIndent(), Toast.LENGTH_SHORT).show()
            }

            override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
                super.onAuthenticationSucceeded(result)
                Toast.makeText(this@BiometricActivity, "지문 인식 성공", Toast.LENGTH_SHORT).show()
            }

            override fun onAuthenticationFailed() {
                super.onAuthenticationFailed()
                Toast.makeText(this@BiometricActivity, "지문 인식 실패", Toast.LENGTH_SHORT).show()
            }

        } )
        return biometricPrompt as BiometricPrompt
    }

    
    /*
    * 생체 인식 인증을 사용할 수 있는지 확인
    * */
    fun authenticateToEncrypt() = with(binding) {

        Log.d(TAG, "authenticateToEncrypt() ")

        var textStatus = ""
        val biometricManager = BiometricManager.from(this@BiometricActivity)
//        when (biometricManager.canAuthenticate(BIOMETRIC_STRONG or DEVICE_CREDENTIAL)) {
        when (biometricManager.canAuthenticate(BiometricManager.Authenticators.BIOMETRIC_WEAK)) {

            //생체 인증 가능
            BiometricManager.BIOMETRIC_SUCCESS -> textStatus = "App can authenticate using biometrics."

            //기기에서 생체 인증을 지원하지 않는 경우
            BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE -> textStatus = "No biometric features available on this device."

            //현재 생체 인증을 사용할 수 없는 경우
            BiometricManager.BIOMETRIC_ERROR_HW_UNAVAILABLE -> textStatus = "Biometric features are currently unavailable."

            //생체 인식 정보가 등록되어 있지 않은 경우
            BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED -> {
                textStatus = "Prompts the user to create credentials that your app accepts."

                val dialogBuilder = AlertDialog.Builder(this@BiometricActivity)
                dialogBuilder
                    .setTitle("나의앱")
                    .setMessage("지문 등록이 필요합니다. 지문등록 설정화면으로 이동하시겠습니까?")
                    .setPositiveButton("확인") { dialog, which -> goBiometricSettings() }
                    .setNegativeButton("취소") {dialog, which -> dialog.cancel() }
                dialogBuilder.show()
            }

            //기타 실패
            else ->  textStatus = "Fail Biometric facility"

        }
        binding.tvStatus.text = textStatus

        //인증 실행하기
        goAuthenticate()
    }
    
    
    /*
    * 생체 인식 인증 실행
    * */
    private fun goAuthenticate() {
        Log.d(TAG, "goAuthenticate - promptInfo : $promptInfo")
        promptInfo?.let {
            biometricPrompt?.authenticate(it)  //인증 실행
        }
    }


    /*
    * 지문 등록 화면으로 이동
    * */
    fun goBiometricSettings() {
        val enrollIntent = Intent(Settings.ACTION_BIOMETRIC_ENROLL).apply {
            putExtra(
                Settings.EXTRA_BIOMETRIC_AUTHENTICATORS_ALLOWED,
                BIOMETRIC_STRONG or DEVICE_CREDENTIAL)
        }
        loginLauncher.launch(enrollIntent)
    }

}
```

btn_bio_set버튼을 클릭 시 authenticateToEncrypt()를 호출하여 생체인증 가능 여부를 판단하고, 확인 후 goAuthenticate() 메소드를 호출하여 biometricPrompt?.authenticate()를 통해 생체인식을 진행한다.   
인증 후 setBiometricPrompt()의 BiometricPrompt.AuthenticationCallback()에서 결과를 콜백 받는다.   
생체 인식 정보가 등록되어 있지 않은 경우, goBiometricSettings()를 호출하여 지문등록 설정화면으로 이동되고, 등록 후 registerForActivityResult()내부에서 다시 authenticateToEncrypt()를 호출하여 상태를 확인한다.
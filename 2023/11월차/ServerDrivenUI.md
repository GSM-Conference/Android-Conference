## ServerDrivenUI
### SDUI란?
화면에 보여지는 UI 렌더링을 클라이언트가 아닌 서버에서 보내주는 response로 화면을 구성하는 방식이다.
### 대표적인 SDUI
- Airbnb의 Ghost Platform
  Section과 Screen으로 구분하여 구성한다.
  GraphQL을 이용하여 response들을 전달하며, iOS, Web과 동일한 response를 사용한다.
  ```graphql
    interface GPResponse {
        sections: [SectionContainer]
        screens: [ScreenContainer]
    }
    ```
    Section은 UI Component를 표현하는 데이터로 어떤 데이터가 표현이 될지도 함께 가지고 있다.
    i18n처리도 되며 포맷도 다 맞추어서 클라이언트에게 전달한다.
    Screen은 화면과 레이아웃이 어떻게 보여질지를 명시하게 되며, 다른 메타데이터들과 로깅 등에 대한 정보도 담고 있다.
    Actions를 통해 Event handling도 제어 가능하다.
    ```kt
    @SectionComponentType(SectionComponentType.TITLE)
    class TitleSectionComponent : SectionComponent<TitleSection>() {
    
        override fun buildSectionUI(section: TitleSection) {
            Text(
                text = section.title,
                style = section.titleStyle
            )

            if (!section.subtitle.isNullOrEmpty()) {
                Text(
                    text = section.subtitle,
                    style = section.subtitleStyle,
                    onClick = {
                        GPActionHandler.handleIAction(section.onSubtitleClickAction)
                    }
                )
            }
        }
    }
    ```
- 요기요의 FontYo
  meta 카테고리에 디자인 정보를 담아서 보내준다.
  앱 버전, os 종류, ab test 여부에 따라서 템플릿을 다르게 보내준다.
  전체 화면을 sdui로 구성하는 것이 아닌 홈화면만 sdui로 구성을 하였다.
### 예시 코드
1. 서버에서 받아올 컴포넌트와 스타일을 정의한다.
  이 때 스타일은 받지 않을 경우를 생각하여 모두 Nullable 처리를 한다.
    ```kt
    enum class ElementType(val typeString: String) {
        BUTTON("Button"),
        TEXT("Text"),
        IMAGE("Image"),
        ROW("Row"),
        COLUMN("Column"),
        CONSTRAINT_LAYOUT("ConstraintLayout"),
        CARD("Card"),
        SPACER("Spacer"),
        LAZY_LIST("LazyList");
    }
    ```
    ```kt
    data class ElementStyle(
        val width: Length? = null,
        val padding: Padding? = null,
        val background : String? = null,
    )
    ```
2. 각 컴포넌트와 스타일을 상속하는 data class를 만든다.
    ```kt
    open class Element(
        open val type: ElementType,
        open val style: ElementStyle?,
    )
    ```
    ```kt
    data class Button(
        val text: Text,
        val buttonStyle: ButtonStyle,
        val color: String,
        override val style: ElementStyle? = null
    ) : Element(ElementType.BUTTON, style)

    enum class ButtonStyle {
        FILLED,
        OUTLINED,
        TEXT
    }
    ```
3. 각 컴포넌트에 따라서 렌더링을 진행한다.
    ```kt
    @Composable
    fun CompositeRenderer(element: Element) {
        when (element) {
            is Text -> {
                TextRenderer(textElement = element)
            }
            is Image -> {
                ImageRenderer(imgElement = element)
            }
            is LazyList -> {
                LazyListRenderer(element = element)
            }
            is com.sgk.sduicore.modal.Column -> {
                ColumnRenderer(element = element)
            }
            is Row -> {
                RowRenderer(element = element)
            }
            is Spacer -> {
                SpacerRenderer(element = element)
            }
            is com.sgk.sduicore.modal.ConstraintLayout -> {
                ConstraintLayoutRenderer( element = element)
            }

            is com.sgk.sduicore.modal.Card -> {
                CardRenderer(element = element)
            }
            is Button -> {
                ButtonRenderer(element = element)
            }
        }
    }
    ```
    ```kt
    @Composable
    fun ButtonRenderer(element: ButtonElement) {
        val modifier : Modifier = element.style?.asModifier() ?: Modifier

        when (element.buttonStyle) {
            FILLED -> Button(
                shape = CircleShape,
                modifier = modifier,
                colors = ButtonDefaults.buttonColors(containerColor = element.color.toColor()),
                onClick = {}
            ) {
                CompositeRenderer(element = element.text)
            }
            OUTLINED -> OutlinedButton(
                shape = CircleShape,
                border = BorderStroke(1.dp, element.color.toColor()),
                modifier = modifier,
                onClick = {}
            ) {
                CompositeRenderer(element = element.text)
            }
            TEXT -> TextButton(
                modifier = modifier,
                colors = ButtonDefaults.buttonColors(containerColor = element.color.toColor()),
                onClick = {}
            ) {
                CompositeRenderer(element = element.text)
            }
        }
    }
    ```
4. modifier를 변환하는 함수를 작성한다.
    ```kt
    fun ElementStyle?.asModifier(): Modifier {
        var modifier: Modifier = Modifier

        if (this == null)
            return modifier

        padding?.let {
            modifier = modifier.padding(
                start = it.left.dp,
                top = it.top.dp,
                end = it.right.dp,
                bottom = it.bottom.dp,
            )
        }

        background?.let {
            modifier = modifier.background(it.toColor())
        }

        val _width = width
        modifier = when (_width) {
            is Length.Max -> {
                modifier.fillMaxWidth()
            }
            is Length.Number -> {
                modifier.width(width = _width.value.dp)
            }
            null -> {
                modifier.wrapContentWidth()
            }
        }

        val _height = height
        modifier = when (_height) {
            is Length.Max -> {
                modifier.fillMaxHeight()
            }
            is Length.Number -> {
                modifier.height(_height.value.dp)
            }
            null -> {
                modifier.wrapContentHeight()
            }
        }

        id?.let {
            modifier = modifier
                .layoutId(it)
                .testTag(it)
        }

        val _style = this
        modifier = modifier
            .semantics {
                width = _style.width
                height = _style.height
                background = _style.background
                padding = _style.padding
                layoutId = _style.id
            }

        return modifier
    }
    ```
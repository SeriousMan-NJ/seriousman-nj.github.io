# 의존성 패키지 설치
Jest로 React를 테스트하기 위해서 다음의 의존성 패키지를 설치해야 한다.
```bash
$ yarn add --dev jest babel-core babel-jest babel-preset-env babel-preset-react react-test-renderer
```
.babelrc도 다음과 같이 구성한다.
```javascript
{
  "presets": ["env", "react", "babel-preset-env"]
}
```
Redux의 store를 테스트하기 위해서 redux-mock-store를 설치한다. fetch를 mock하기 위해 fetch-mock도 설치한다.
```bash
$ yarn add --dev redux-mock-store fetch-mock
```
package.json에 스크립트 부분을 추가한다.
```javascript
{
  ...
  "scripts": {
    ...
    "test": "jest",
    "test:watch": "npm test -- --watch"
  },
  ...
}
```
# Redux Unit Test
## Actions Creators

<!-- {% raw %} -->
```javascript
import { RECEIVE_POST_LIST } from './constants'

export function receivePostList(postList) {
  return {
    type: RECEIVE_POST_LIST,
    postList
  }
}
```
<!-- {% endraw %} -->

위에서 receivePostList는 다음과 같이 테스트할 수 있다.

<!-- {% raw %} -->
```javascript
import * as actions from './actions'
import * as types from './constants'

describe('actions', () => {
  it('should create an action to fetch posts', () => {
    const postList = [{ title: '테스트 제목', content: '테스트 내용'}]
    const expectedAction = {
      type: types.RECEIVE_POST_LIST,
      postList
    }
    expect(actions.receivePostList(postList)).toEqual(expectedAction)
  })
})
```
<!-- {% endraw %} -->

## Async Action Creators

<!-- {% raw %} -->
```javascript
import { RECEIVE_POST_LIST } from './constants'

// recievePostList는 위에서 테스트하는 방법을 보였다.
function receivePostList(postList) {
  return {
    type: RECEIVE_POST_LIST,
    postList
  }
}

// fetch를 사용한 async action creator이다.
export function fetchPostList() {
  return dispatch => {
    return fetch(`http://homestead.global.com/api/post`)
      .then(response => response.json())
      .then(json => dispatch(receivePostList(json)))
  }
}
```
<!-- {% endraw %} -->

위에서 thunk를 사용하는 fetchPostList를 테스트하기 위해서 store에 thunk middleware를 넣어서 mocking해야 한다. fetch가 사용되므로, 네트워크를 이용하지 않도록 fetch-mock을 이용해 fetch를 mocking한다.

<!-- {% raw %} -->
```javascript
import configureMockStore from 'redux-mock-store'
import thunk from 'redux-thunk'
import * as actions from './actions'
import * as types from './constants'
import fetchMock from 'fetch-mock'

const middleWares = [thunk]
const mockStore = configureMockStore(middleWares)

describe('async actions', () => {
  // 매 테스트마다 fetchMock을 초기화하고, fetch를 원래대로 되돌린다.
  afterEach(() => {
    fetchMock.reset()
    fetchMock.restore()
  })

  it('dispatch receivePostList action', () => {
    const postList = [{ title: '테스트 제목', content: '테스트 내용'}]
    fetchMock.getOnce(`http://homestead.global.com/api/post`, { body: postList }) // fetch를 mocking한다.

    const expectedActions = [
      { type: types.RECEIVE_POST_LIST, postList }
    ]
    const store = mockStore({ postList: [] })

    return store.dispatch(actions.fetchPostList(postList)).then(() => {
      expect(store.getActions()).toEqual(expectedActions)
    })
  })
})
```
<!-- {% endraw %} -->

## Reducers

<!-- {% raw %} -->
```javascript
import { RECEIVE_POST_LIST } from './constants'

export function postList(state=[], action) {
  switch (action.type) {
    case RECEIVE_POST_LIST:
      return [
        ...action.postList
      ]
    default:
      return state
  }
}
```
<!-- {% endraw %} -->

위에서 postList는 다음과 같이 테스트한다.

<!-- {% raw %} -->
```javascript
import * as reducers from './reducers'
import * as types from './constants'

describe('postList reducer', () => {
  it('should return the initial state', () => {
    expect(reducers.postList(undefined, {})).toEqual([])
  })

  it('should handle RECEIVE_POST_LIST', () => {
    const postList = [{ title: '테스트 제목', content: '테스트 내용'}]

    expect(
      reducers.postList([], {
        type: types.RECEIVE_POST_LIST,
        postList
      })
    ).toEqual(postList)
  })
})
```
<!-- {% endraw %} -->

## React Component Unit Test
Enzyme을 이용해서 테스트할 것이다. Jest에서 Enzyme을 사용하기 위해서 다음을 설치한다.
```bash
$ yarn add --dev enzyme enzyme-adapter-react-16
```
그리고 Enzyme을 사용할 때 다음과 같이 구성해야 한다.

<!-- {% raw %} -->
```javascript
import React from 'react'
...
import Enzyme, { mount, shallow } from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'

Enzyme.configure({ adapter: new Adapter() });
```
<!-- {% endraw %} -->

테스트할 Component를 다음과 같다.

<!-- {% raw %} -->
```javascript
import React, { Component } from 'react'
import App from '../../components/App'
import { connect } from 'react-redux'
import { fetchPostList } from './actions'
import { Link } from 'react-router-dom'

import { Grid, GridColumn as Column } from '@progress/kendo-react-grid'

// 테스트를 위해 export함
export function mapStateToProps(state) {
  const { postList } = state

  return {
    postList
  }
}
// 테스트를 위해 export함
export function mapDispatchToProps(dispatch) {
  return {
    fetchPostList: () => {
      dispatch(fetchPostList())
    }
  }
}
// 테스트를 위해 export함
export class PostList extends Component {
  componentDidMount() {
    const { fetchPostList } = this.props
    fetchPostList()
  }
  render() {
    const { postList } = this.props

    return (
      <App
        title={
          <div>
            <h1>게시글 목록!</h1>
          </div>
        }
        content={postList.length > 0 &&
          <div>
            <h2>요약 보기</h2>
            <Grid
              style={{ maxHeight: '400px' }}
              data={postList}
            >
              <Column field="title" title="ID" width="100px" />
              <Column field="content" title="Name" width="250px" />
            </Grid>
            <h2>자세히 보기</h2>
            {postList.map((p) => {
              return (
                <div key={p.id} style={{paddingBottom: '2em'}}>
                  <h4>{p.title}</h4>
                  <div dangerouslySetInnerHTML={{__html: p.content}} />
                </div>
              )
            })}
          </div>
        }
      />
    )
  }
}
export default connect(mapStateToProps, mapDispatchToProps)(PostList)
```
<!-- {% endraw %} -->

**강조1**: mapStateToProps와 mapDispatchToProps를 테스트하기 위해 export 했다.

**강조2**: presentational component만 테스트하기 위해 PostList도 export 했다.


다음은 테스트 파일이다. PostList 테스트와 Connected(Container) component 테스트가 함께 들어있다.

<!-- {% raw %} -->
```javascript
import React from 'react'
import Enzyme, { mount, shallow } from 'enzyme'
import
  ConnectedPostList,
  { PostList, mapStateToProps, mapDispatchToProps } from './index'
import * as actions from './actions'
import Adapter from 'enzyme-adapter-react-16'

Enzyme.configure({ adapter: new Adapter() });

function setup() {
  const props = {
    fetchPostList: jest.fn(),
    postList: [{ id: 1, title: '테스트 제목', content: '테스트 본문입니다.' }]
  }

  const enzymeWrapper = mount(<PostList {...props} />)

  return {
    props,
    enzymeWrapper
  }
}

describe('component', () => {
  it('should render title and content', () => {
    const { props, enzymeWrapper } = setup()

    expect(props.fetchPostList.mock.calls.length).toBe(1)
    expect(enzymeWrapper.find('h1').text()).toBe('게시글 목록!')
  })
})

describe('connectedComponent', () => {
  it('mapDispatchToProps', () => {
    const dispatch = jest.fn()
	// actions.fetchPostList는 모듈의 함수이기 때문에 테스트가 끝나고 복원시켜주어야 한다.
	// jest.fn()은 복원 방법을 제공하지 않으므로 jest.fn() 대신 jest.spyOn() 함수를 이용한다.
	// jest.spyOn() 함수는 원래 함수를 호출하는데 이를 막기 위해 mockImplementation()을 통해 빈 구현체를 제공할 수 있다.
    const fetchPostList = jest.spyOn(actions, 'fetchPostList').mockImplementation()
    mapDispatchToProps(dispatch).fetchPostList()
    expect(dispatch.mock.calls.length).toBe(1)
    expect(fetchPostList.mock.calls.length).toBe(1)
    fetchPostList.mockReset()
    fetchPostList.mockRestore()
  })

  it('mapStateToProps', () => {
    const postList = [{ id: 1, title: '테스트 제목', content: '테스트 내용'}]
    const state = { postList }
    expect(mapStateToProps(state)).toEqual({ postList })
  })
})
```
<!-- {% endraw %} -->

**주의**: jest.fn()을 사용할 때와 jest.spyOn()을 사용해야 할 때를 구분한다.

Connected component Integration Test
하는 방법을 찾지 못했다.

작업했던 코드조각은 아래와 같다. connectedComponent의 'should render posts' 테스트에 해당한다.

<!-- {% raw %} -->
```javascript
import React from 'react'
import Enzyme, { mount, shallow } from 'enzyme'
import
  ConnectedPostList,
  { PostList, mapStateToProps, mapDispatchToProps } from './index'
import * as actions from './actions'
import Adapter from 'enzyme-adapter-react-16'
import { Provider } from 'react-redux'
import configureMockStore from 'redux-mock-store'
import thunk from 'redux-thunk'
import fetchMock from 'fetch-mock'

const middleWares = [thunk]
const mockStore = configureMockStore(middleWares)

Enzyme.configure({ adapter: new Adapter() });

function setup() {
  const props = {
    fetchPostList: jest.fn(),
    postList: [{ id: 1, title: '테스트 제목', content: '테스트 본문입니다.' }]
  }

  const enzymeWrapper = mount(<PostList {...props} />)

  return {
    props,
    enzymeWrapper
  }
}

function setup2() {
  const store = mockStore({ postList: [] })
  const enzymeWrapper = mount(
    // <Provider store={store}>
      <ConnectedPostList store={store}/>
    // </Provider>
  )
  return {
    enzymeWrapper
  }
}

describe('component', () => {
  it('should render title and content', () => {
    const { props, enzymeWrapper } = setup()

    expect(props.fetchPostList.mock.calls.length).toBe(1)
    expect(enzymeWrapper.find('h1').text()).toBe('게시글 목록!')
  })
})

describe('connectedComponent', () => {
  afterEach(() => {
    fetchMock.reset()
    fetchMock.restore()
  })

  it('mapDispatchToProps', () => {
    const dispatch = jest.fn()
    const fetchPostList = jest.spyOn(actions, 'fetchPostList').mockImplementation()
    mapDispatchToProps(dispatch).fetchPostList()
    expect(dispatch.mock.calls.length).toBe(1)
    expect(fetchPostList.mock.calls.length).toBe(1)
    fetchPostList.mockReset()
    fetchPostList.mockRestore()
  })

  it('mapStateToProps', () => {
    const postList = [{ id: 1, title: '테스트 제목', content: '테스트 내용'}]
    const state = { postList }
    expect(mapStateToProps(state)).toEqual({ postList })
  })

  it('should render posts', () => {
    const postList = [{ id: 1, title: '테스트 제목', content: '테스트 내용'}]
    fetchMock.getOnce(`http://homestead.global.com/api/post`, { body: postList })
    const { enzymeWrapper } = setup2()
    // enzymeWrapper.update()
    // console.log(store.getState())
    console.log(enzymeWrapper.props().store.getActions())
  })
})
```
<!-- {% endraw %} -->

# 참고문헌
Jest.fn(), jest.spyOn(): https://facebook.github.io/jest/docs/en/jest-object.html

Jest mock functions(): https://facebook.github.io/jest/docs/en/mock-function-api.html

Enzyme working with React 16: http://airbnb.io/enzyme/docs/installation/react-16.html

Jest - Testing React Apps: https://facebook.github.io/jest/docs/en/tutorial-react.html

redux & react 테스트: https://redux.js.org/recipes/writing-tests

mapStateToProps, mapDispatchToPros를 모킹하는 3가지 방법: https://jsramblings.com/2018/01/15/3-ways-to-test-mapStateToProps-and-mapDispatchToProps.html

thunk가 있는 mapDispatchToProps 테스트: https://stackoverflow.com/questions/41633297/how-to-unit-test-mapdispatchtoprops-with-thunk-action

Node 싱글톤 모듈: https://medium.com/@lazlojuly/are-node-js-modules-singletons-764ae97519af

Sinon 모킹 라이브러리: http://sinonjs.org

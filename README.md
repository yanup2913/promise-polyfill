# promise-polyfill



function PromisePolyfill(executer) {

	let onResolve, onReject;
	let fulfilled = false,
	rejected = false,
	resolveCalled = false,
	rejectCalled = false,
	resolveValue, rejectValue;
	
	function resolve(val) {

		fulfilled = true;
		resolveValue = val;

		if(typeof onResolve === "function") {
			onResolve(val);
			resolveCalled = true;
		}
		
	}

	function reject(val) {

		rejected = true;
		rejectValue = val;

		if(typeof onReject === "function") {
			onReject(val);
			rejectCalled = true;
		}
		
	}


  	this.then = function(callback) {
		onResolve = callback;

		if(fulfilled && !resolveCalled) {
			resolveCalled = true;
			onResolve(resolveValue);
		}
		return this;
	}

	this.catch = function(callback) {
		onReject = callback;

		if(rejected && !rejectCalled) {
			rejectCalled = true;
			onReject(rejectValue);
		}
		return this;
	}

	executer(resolve, reject);
}

PromisePolyfill.resolve = (val) => {

	new PromisePolyfill((resolve) => {resolve(val)});
}

PromisePolyfill.reject = (reason) => {

	new PromisePolyfill((resolve, reject) => {reject(reason)})
}

PromisePolyfill.all = (promises) => {
	
	let result = [];
	let fulfilledPromise = [];

	function executer(resolve, reject) {
		promises.forEach((promise, index) => {
			return promise.then((val) => {
				fulfilledPromise.push(true);
				result[index] = val;
				
				if(fulfilledPromise.length === promises.length) {
					return resolve(result);
				}
			}).catch((error) => {
				return reject(error);
			})
		})
	}	

	return new PromisePolyfill(executer);
}



